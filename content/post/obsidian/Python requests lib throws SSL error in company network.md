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

Python requests lib is not using the certificate in system certificate manager, which means it can not find a usable certificate like your browser.
So, the solution is to add the root certificate to the PEM file that python is using. Normally the root certificate is issued by your company, which can be checked in your browser.
Then a working verification chain for python requests lib can be established.
See a solution directly: [[#Solution 2 Alternative fix using a lib]]. I did not not try with this. If it does not work, use [[#Solution 1 Manual fix]].
This problem does not happen on my personal computer, because there is no proxy generating a self-signed certificate.

Python requests 库没有使用系统自带的证书管理器。这意味着他不能像浏览器一样找到一个有效的证书。
所以，解决方案是添加一个可用的根证书在python正在用的PEM文件。这个根证书通常是你的公司签发的，可以在你的浏览器中查看。
这样，一个Python requests 库可用的认证链就建立了。
方法直接看: [[#Solution 2 Alternative fix using a lib]]。 我没试这个，不过应该可以。如果不行，就看 [[#Solution 1 Manual fix]]。
在我个人电脑上没这个问题，应该是因为没有代理在中间生成了一个自签名证书。

## Problem description

Sample code:
```python
import requests
import certifi

print(certifi.where())
response = requests.get('https://chroma-onnx-models.s3.amazonaws.com/all-MiniLM-L6-v2/onnx.tar.gz', verify=certifi.where())
```

Run this code, returning:
```bash
  ......
  File "C:\Users\lizh\AppData\Local\Anaconda3-2024.02\Lib\site-packages\requests\adapters.py", line 620, in send
    raise SSLError(e, request=request)
requests.exceptions.SSLError: HTTPSConnectionPool(host='chroma-onnx-models.s3.amazonaws.com', port=443): Max retries exceeded with url: /all-MiniLM-L6-v2/onnx.tar.gz (Caused by SSLError(SSLCertVerificationError(1, '[SSL: CERTIFICATE_VERIFY_FAILED] certificate verify failed: self-signed certificate in certificate chain (_ssl.c:1006)')))
```

However, if you use the URL in web browser, there is no problem. This means the certificate can be verified in some way, which the python requests lib is not using.

## Some trials

Some references (e.g., [Fixing Python Requests SSLError: CERTIFICATE_VERIFY_FAILED - Sling Academy](https://www.slingacademy.com/article/fixing-python-requests-sslerror-certificate-verify-failed/)) recommend you to "specify the certificate" or "disable SSL Verification".
The latter one can be a workaround but not good enough.
The former can not work for me in beginning, because I misunderstood it.

I tried to download the certificate of the target website from web browser, and then add it to pem file of python certifi lib (`...\Lib\site-packages\certifi\cacert.pem`). But it still fails. (Actually it is close. If I add the root certificate of my company, then it will work.)
I also installed the downloaded certificate to windows certificate manager (certmgr). It still not works.

## Solutions

### Understand the reason

In my understanding, the key point is why browser can work, but python requests can not?
Specifically, why does python throw “Certificate validation failed: self-signed certificate in the certificate chain” but browser do not.
Apparently, they use different certificate chains: 
- Browser uses the system certificate manager;
- Python requests lib uses pem file of certifi lib (`...\Lib\site-packages\certifi\cacert.pem`).

Regarding the reason why this does not happen on my personal computer, I think it is because the company's proxy generate a self-signed certificate.

#### How company proxy affects the HTTPS

(From copilot)

在公司网络中，代理服务器可能会拦截和检查 HTTPS 流量，这种行为通常被称为“中间人攻击”（Man-in-the-Middle Attack, MITM），但在企业环境中，这种技术被合法地用于监控和保护网络安全。以下是详细解释：

##### 1. **代理服务器的工作原理**

代理服务器充当客户端（如你的计算机）和目标服务器（如你访问的网站）之间的中介。当你在公司网络中访问 HTTPS 网站时，请求首先发送到代理服务器，然后代理服务器再将请求转发给目标服务器。

##### 2. **SSL/TLS 加密**

HTTPS 使用 SSL/TLS 协议来加密数据传输，确保数据在传输过程中不被窃取或篡改。正常情况下，客户端和目标服务器之间建立一个安全的加密通道。

##### 3. **代理服务器的拦截和检查**

为了检查 HTTPS 流量，代理服务器会在客户端和目标服务器之间插入自己。具体步骤如下：

1. **代理服务器生成自签名证书**：代理服务器会为目标网站生成一个自签名证书，并将其发送给客户端。
2. **客户端信任代理服务器的证书**：如果客户端信任代理服务器的证书（通常通过在客户端设备上预装代理服务器的根证书），客户端会接受这个自签名证书，并与代理服务器建立加密连接。
3. **代理服务器与目标服务器建立连接**：代理服务器再与目标服务器建立一个独立的加密连接。

##### 4. **证书验证失败的原因**

由于代理服务器使用的是自签名证书，而不是目标服务器的真实证书，客户端在验证证书时可能会失败，具体原因包括：

- **缺少信任**：如果客户端没有预装代理服务器的根证书，客户端会认为代理服务器的证书不可信，从而导致 SSL 证书验证失败。
- **证书链不完整**：代理服务器生成的自签名证书可能不包含完整的证书链，导致验证失败。
- **证书不匹配**：客户端期望的证书与代理服务器提供的证书不匹配，导致验证失败。

##### 解决方法

- **安装代理服务器的根证书**：在客户端设备上安装并信任代理服务器的根证书。
- **配置代理设置**：确保在使用 `requests` 库时正确配置代理设置，并指定正确的证书路径。

### Solution 1: Manual fix

1. Check what is the working verification chain in browser: [How to View SSL Certificate Details in Each Browser (globalsign.com)](https://www.globalsign.com/en/blog/how-to-view-ssl-certificate-details)
2. Know what is the root certificate in your company, find it, and export it from system certificate manager (certmgr).
3. Open that certificate, copy and paste its content to the end of pem file of certifi lib (`...\Lib\site-packages\certifi\cacert.pem`).
4. Now, your python request should work.

### Solution 2: Alternative fix using a lib

It seems all the above can be done by installing a lib using `pip install pip_system_certs`.
I did not test it, but it makes sense.
