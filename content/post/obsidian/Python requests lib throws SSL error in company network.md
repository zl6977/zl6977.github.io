---
date: 2024-09-03 14:30:16 +02:00
tags:
  - 2024-09
title: Python requests lib throws SSL error in company network
TOC: true
draft: false
---

# Python requests lib throws SSL error in company network
## Short version/简短版

Accessing an HTTPS URL using the Python requests library throws a self-signed error. However, accessing the URL in a browser is fine, and also fine on a private computer.
Based on Copilot's answer, I understand that the root cause is proxy server interception and different clients using different certificate storage locations. This means that different certificate chains are created in different scenarios, some are valid and some are not.
The proxy server modifies the original certificate returned from the HTTPS server to generate a new certificate. This new certificate points to the proxy, and eventually to company's root certificate. 
This root certificate is self-signed and exists in the system's own certificate manager, but not in the certificate manager used by the Python requests library. That's why the Python requests library is prompting for a “self-signed cert error”.
So the solution is to add your company's root certificate (which is the self-signed certificate that the requests lib prompts for) to the PEM file that Python is using, in order to make the new certificate chain valid.
A simple solution is: [[#Solution 2 Installing a lib]]. I did not try this, but it should work. If it doesn't work, look at [[#Solution 1 Manual fix]].

使用 Python requests 库访问一个 HTTPS URL 会报错，提示自签名错误。但是在浏览器中访问这个 URL 就没问题，在私人电脑上也没问题。
根据Copilot的回答，我理解的根本原因是代理服务器拦截和不同客户端使用不同的证书储存位置。这意味着不同场景中建立的证书链是不一样的，有的有效，有的无效。
代理服务器会修改HTTPS服务器返回的原证书，生成一个新证书。这个新证书指向代理服务器，并最终指向公司的根证书。
这个根证书是自签名的，存在于系统自带的证书管理器，但不在Python requests库使用的证书管理器中。所以Python requests库会提示“自签名错误”。
所以，解决方案是添加你公司的根证书（也就是requests lib提示的那个自签名证书）到 Python 正在用的 PEM 文件，目的是使新证书链有效。
方法直接看: [[#Solution 2 Installing a lib]]。 我没试这个，不过应该可以。如果不行，就看 [[#Solution 1 Manual fix]]。

## Problem description
### Recurrence 
Sample code:
```python
import requests
import certifi

print(certifi.where())
response = requests.get('https://chroma-onnx-models.s3.amazonaws.com/all-MiniLM-L6-v2/onnx.tar.gz', verify=certifi.where())
```

The code returns:
```bash
  ......
  File "C:\Users\zzz\AppData\Local\Anaconda3-2024.02\Lib\site-packages\requests\adapters.py", line 620, in send
    raise SSLError(e, request=request)
requests.exceptions.SSLError: HTTPSConnectionPool(host='chroma-onnx-models.s3.amazonaws.com', port=443): Max retries exceeded with url: /all-MiniLM-L6-v2/onnx.tar.gz (Caused by SSLError(SSLCertVerificationError(1, '[SSL: CERTIFICATE_VERIFY_FAILED] certificate verify failed: self-signed certificate in certificate chain (_ssl.c:1006)')))
```

However, if you use the URL in web browser, there is no problem. This means the certificate can be verified in some way, which the python requests lib is not using.

### Some trials
Some references (e.g., [Fixing Python Requests SSLError: CERTIFICATE_VERIFY_FAILED - Sling Academy](https://www.slingacademy.com/article/fixing-python-requests-sslerror-certificate-verify-failed/)) recommend you to "specify the certificate" or "disable SSL Verification".
The latter one can be a workaround but not good enough.
The former can not work for me in beginning, because I was specifying the target server's cert.

I tried to download the certificate of the target website from web browser, and then add it to pem file of python certifi lib (`...\Lib\site-packages\certifi\cacert.pem`). But it still fails. (Actually it is close. If I add the root certificate of my company, then it will work.)
I also installed the downloaded certificate to windows certificate manager (certmgr.msc), and it still not works.

I also used openssl to check the certificate chain: `openssl s_client -connect example.com:443 -showcerts`.
The returning on company computer is like:
```bash
CONNECTED(000001C0)
---
Certificate chain
 0 s:CN = *.s3.amazonaws.com
   i:C = NO, O = Company name, OU = Company IT, CN = Company proxy
-----END CERTIFICATE-----
 1 s:C = NO, O = Company name, OU = Company IT, CN = Company proxy
   i:DC = no, DC = company, DC = ad, CN = Company Issuing CA
-----END CERTIFICATE-----
 2 s:DC = no, DC = company, DC = ad, CN = Company Issuing CA
   i:CN = Company Root CA
-----END CERTIFICATE-----
 3 s:CN = Company Root CA
   i:CN = Company Root CA
-----END CERTIFICATE-----

Verification error: self-signed certificate in certificate chain
```

The returning on personal computer is:
```bash
CONNECTED(000001A8)
---
Certificate chain
 0 s:CN=s3.amazonaws.com
   i:C=US, O=Amazon, CN=Amazon RSA 2048 M01
-----END CERTIFICATE-----
 1 s:C=US, O=Amazon, CN=Amazon RSA 2048 M01
   i:C=US, O=Amazon, CN=Amazon Root CA 1
-----END CERTIFICATE-----
 2 s:C=US, O=Amazon, CN=Amazon Root CA 1
   i:C=US, ST=Arizona, L=Scottsdale, O=Starfield Technologies, Inc., CN=Starfield Services Root Certificate Authority - G2
-----END CERTIFICATE-----
 3 s:C=US, ST=Arizona, L=Scottsdale, O=Starfield Technologies, Inc., CN=Starfield Services Root Certificate Authority - G2
   i:C=US, O=Starfield Technologies, Inc., OU=Starfield Class 2 Certification Authority
-----END CERTIFICATE-----

Verification: OK
```

## Understand the reason

For me, the questions are:
- Why it did not happen on personal computer, no matter for Python requests or browser?
- Why browser can work, but Python requests can not on company computer?
    - Specifically, why does Python throw “Certificate validation failed: self-signed certificate in the certificate chain” but browser do not.
And the key point is to know what the certificate chains are in each case, and how these chains are formed.

There are 4 cases:
- On personal computer (PC):

    1. Python requests (PPC)
    2. Browser (BPC)
- On company computer (CC):

    3. Python requests (PCC)
    4. Browser (BCC)

Apparently, they use different certificate chains. The root reasons are proxy interception and the different root cert location: 
- Browser uses the system certificate manager (certmgr.msc on windows);
- Python requests lib uses PEM file of certifi lib (`...\Lib\site-packages\certifi\cacert.pem`).

For each case, the certificate chains are:
1. PPC: no proxy, target server cert -> intermediate certs ... -> root cert in certifi PEM file. (Work normally, as the root cert is issued by a [well-known issuer](https://ccadb.my.salesforce-sites.com/mozilla/CAInformationReport), already existing in this location)
2. BPC: no proxy, target server cert -> intermediate certs ... -> root cert in system certificate manager. (Work normally, as the root cert is issued by a [well-known issuer](https://ccadb.my.salesforce-sites.com/mozilla/CAInformationReport), already existing in this location)
3. PCC: proxy "fakes" a new cert of target server.
    1. target server cert -> intermediate certs ... -> root cert on proxy server. (Work normally, as the root cert is issued by a [well-known issuer](https://ccadb.my.salesforce-sites.com/mozilla/CAInformationReport), already existing in this location)
    2. target server cert (issued/modified/"faked" by proxy) -> certs of proxy server -> company root cert. (Error, as it is NOT in the ==default certifi PEM file==, thus throwing "self-signed cert error").
4. BCC: proxy "fakes" a new cert of target server.
    1. target server cert -> intermediate certs ... -> root cert on proxy server. (Work normally, as the root cert is issued by a [well-known issuer](https://ccadb.my.salesforce-sites.com/mozilla/CAInformationReport), already existing in this location)
    2. target server cert (issued/modified/"faked" by proxy) -> certs of proxy server -> company root cert. (Work normally, as it is pre-installed in your ==system certificate manager== by your IT).

## Solutions
The idea is to form a valid certificate chain, fixing the part where the chain is broken.

### Solution 1: Manual fix
1. Check what is the working verification chain in browser: [How to View SSL Certificate Details in Each Browser (globalsign.com)](https://www.globalsign.com/en/blog/how-to-view-ssl-certificate-details)
2. Know what is the root certificate in your company, find it, and export it from system certificate manager (certmgr).
3. Open that certificate, copy and paste its content to the end of pem file of certifi lib (`...\Lib\site-packages\certifi\cacert.pem`).
4. Now, your python requests should work.

- Additional manual fix:
There are other libraries that use different pem files. If you encounter similar problems when using them, you can fix them in the same way.
Search the python folder for all files with the suffix “pem” and patch them with the root certificate.
```bash
"C:\Users\zzz\AppData\Local\Anaconda3-2024.02\pkgs\pip-23.3.1-py311haa95532_0\Lib\site-packages\pip\_vendor\certifi\cacert.pem"
"C:\Users\zzz\AppData\Local\Anaconda3-2024.02\Library\ssl\cacert.pem"
"C:\Users\zzz\AppData\Local\Anaconda3-2024.02\pkgs\ca-certificates-2023.12.12-haa95532_0\Library\ssl\cacert.pem"
"C:\Users\zzz\AppData\Local\Anaconda3-2024.02\pkgs\botocore-1.31.64-py311haa95532_0\Lib\site-packages\botocore\cacert.pem"
"C:\Users\zzz\AppData\Local\Anaconda3-2024.02\Lib\site-packages\botocore\cacert.pem"
"C:\Users\zzz\AppData\Local\Anaconda3-2024.02\Lib\site-packages\grpc\_cython\_credentials\roots.pem"
"C:\Users\zzz\AppData\Local\Anaconda3-2024.02\Lib\site-packages\certifi\cacert.pem"
"C:\Users\zzz\AppData\Local\Anaconda3-2024.02\pkgs\certifi-2024.2.2-py311haa95532_0\Lib\site-packages\certifi\cacert.pem"
"C:\Users\zzz\AppData\Local\Anaconda3-2024.02\Library\share\grpc\roots.pem"
"C:\Users\zzz\AppData\Local\Anaconda3-2024.02\pkgs\grpc-cpp-1.48.2-hfe90ff0_1\Library\share\grpc\roots.pem"
```

### Solution 2: Installing a lib
It seems the fixes to Python requests lib can be done by installing a lib using `pip install pip_system_certs`.
I did not test it, but it makes sense.

Regarding to the fixes to other PEM files, I am not sure if they can be done by this lib.

## Expanded reading (From copilot)
### 详细解释一下，为什么“在公司网络中，代理服务器可能会拦截和检查 HTTPS 流量，这可能会导致 SSL 证书验证失败。”
在公司网络中，代理服务器可能会拦截和检查 HTTPS 流量，这种行为通常被称为“中间人攻击”（Man-in-the-Middle Attack, MITM），但在企业环境中，这种技术被合法地用于监控和保护网络安全。以下是详细解释：

#### 1. **代理服务器的工作原理**
代理服务器充当客户端（如你的计算机）和目标服务器（如你访问的网站）之间的中介。当你在公司网络中访问 HTTPS 网站时，请求首先发送到代理服务器，然后代理服务器再将请求转发给目标服务器。

#### 2. **SSL/TLS 加密**
HTTPS 使用 SSL/TLS 协议来加密数据传输，确保数据在传输过程中不被窃取或篡改。正常情况下，客户端和目标服务器之间建立一个安全的加密通道。

#### 3. **代理服务器的拦截和检查**
为了检查 HTTPS 流量，代理服务器会在客户端和目标服务器之间插入自己。具体步骤如下：
1. **代理服务器生成自签名证书**：代理服务器会为目标网站生成一个==自签名证书==(ZZZ: 不一定是自签名，更可能是中间证书，指向公司的自签名的根证书)，并将其发送给客户端。
2. **客户端信任代理服务器的证书**：如果客户端信任代理服务器的证书（通常通过在客户端设备上预装代理服务器的根证书），客户端会接受这个自签名证书，并与代理服务器建立加密连接。
3. **代理服务器与目标服务器建立连接**：代理服务器再与目标服务器建立一个独立的加密连接。

#### 4. **证书验证失败的原因**
由于代理服务器使用的是自签名证书，而不是目标服务器的真实证书，客户端在验证证书时可能会失败，具体原因包括：
- **缺少信任**：如果客户端没有预装代理服务器的根证书，客户端会认为代理服务器的证书不可信，从而导致 SSL 证书验证失败。
- **证书链不完整**：代理服务器生成的自签名证书可能不包含完整的证书链，导致验证失败。
- **证书不匹配**：客户端期望的证书与代理服务器提供的证书不匹配，导致验证失败。

#### 解决方法
- **安装代理服务器的根证书**：在客户端设备上安装并信任代理服务器的根证书。
- **配置代理设置**：确保在使用 `requests` 库时正确配置代理设置，并指定正确的证书路径。

### 代理服务器会为目标服务器生成一个新的证书签名请求
是的，你理解得很正确。代理服务器在拦截和检查 HTTPS 流量时，会为目标服务器生成一个新的证书签名请求（CSR），然后使用自己的证书颁发机构（CA）对其进行签发。具体步骤如下：

#### 代理服务器的操作步骤

1. **生成 CSR**：
   - 当客户端请求一个 HTTPS 网站时，代理服务器会生成一个新的 CSR，这个 CSR 包含了目标服务器的域名信息和公钥。

2. **签发证书**：
   - 代理服务器使用自己的中间证书或根证书对生成的 CSR 进行签发，生成一个新的服务器证书。

3. **返回给客户端**：
   - 代理服务器将签署好的新证书返回给客户端。这个新证书的签发人是代理服务器的 CA，而不是目标服务器的原始 CA。

#### 证书链的变化
- **原始证书链**：
  - 服务器A的证书 -> 中间证书 -> 根证书（受信任的CA）

- **代理服务器修改后的证书链**：
  - 代理服务器生成的证书 -> 代理服务器的中间证书（如果有） -> 代理服务器的根证书

#### 客户端验证
- 客户端会验证代理服务器返回的证书链。如果客户端信任代理服务器的根证书或中间证书，SSL验证就会成功。

通过这种方式，代理服务器可以拦截和检查 HTTPS 流量，同时保持客户端与目标服务器之间的加密连接。如果你有更多问题或需要进一步的帮助，请告诉我！
