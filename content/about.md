---
date: "2014-04-09"
title: "About this Github page"
---

It is built based on Hugo framework and Cupper theme, compiled and deployed by Github workflow [github-pages-action](https://github.com/marketplace/actions/github-pages-action).
I randomly share some Obsidian notes here.

## Hugo framework
Hugo is the **world’s fastest framework for building websites**. It is written in Go.

It makes use of a variety of open source projects including:

* https://github.com/russross/blackfriday
* https://github.com/alecthomas/chroma
* https://github.com/muesli/smartcrop
* https://github.com/spf13/cobra
* https://github.com/spf13/viper

Learn more and contribute on [GitHub](https://github.com/gohugoio).

## Cupper theme
The cupper theme is an accessibility-friendly Hugo theme, ported from the original Cupper project.    

It is clean and elegant, making the user focus on the words.

Find it [here](https://github.com/zwbetz-gh/cupper-hugo-theme).

## Some addtional adjustment
1. Add plantUML support. (添加plantUML支持)
    - 网上的goldmark-plantUML是针对本地版go-goldmark用的，不易部署在github action上
    - 采用https://mogeko.me/posts/zh-cn/083/ 的解决办法。即将plantUML代码转码，然后生成URL，作为img的src。
    - 需要对上述代码做小修改：
    - `code.innerText` -> `code.innerHTML`
        - 实体名称替换：`code.innerHTML.replaceAll('&lt;', '<').replaceAll('&gt;', '>');`
        - 调整宽度：`image.style = "max-height:none";`
    - 代码如下，添加至 `themes\cupper-hugo-theme\layouts\partials\script.html`。
2. Hugo不支持文本高亮（即使用`"=="`）
    - 在主题js中添加了代码，将其转成`<mark></mark>`。
    - 转完后暗色主题失效。将替换范围由原先的body部分缩小到main部分。
    - 代码如下，添加至 `themes\cupper-hugo-theme\layouts\partials\script.html`。
3. Github 部署。
    - 方式1：本地生成静态网页，push上去。
    - 方式2：部署workflow action，生成到gh-page分支，让github使用该分支发布网站。
4. 代码行间距过大。添加js代码解决：display: flex -> inline.
    - 代码如下，添加至 `themes\cupper-hugo-theme\layouts\partials\script.html`。
5. Hugo 升级后不支持旧版cupper theme中的某些特征，将它们在各个模板文件中替换即可通过新版hugo (tested on hugo v0.134.0)的编译。
    - `.Site.DisqusShortname` -> `.Site.Config.Services.Disqus.Shortname`
    - `.Site.IsServer` -> `hugo.IsServer`
        - `$.Site.IsServer` -> `hugo.IsServer` in `themes/cupper-hugo-theme/layouts/partials/disqus.html`
    - `.Site.GoogleAnalytics` -> `.Site.Config.Services.GoogleAnalytics.ID`


```html
{{ if or .Page.Params.plantuml .Site.Params.plantuml }}
<!-- PlantUML -->
<script src="https://cdn.jsdelivr.net/gh/jmnote/plantuml-encoder@1.2.4/dist/plantuml-encoder.min.js"
    integrity="sha256-Qsk2KRBCN5qVZX7B+8+2IvQl1Aqc723qV1tBCQaVoqo=" crossorigin="anonymous"></script>
<script>
    (function () {
        let plantumlPrefix = "language-plantuml";
        Array.prototype.forEach.call(document.querySelectorAll("[class^=" + plantumlPrefix + "]"), function (code) {
            let image = document.createElement("IMG");
            image.loading = 'lazy'; // Lazy loading
            let plantumlStr = decodeURI(code.innerHTML).replaceAll('&lt;', '<').replaceAll('&gt;', '>');
            image.src = 'http://www.plantuml.com/plantuml/svg/~1' + plantumlEncoder.encode(plantumlStr);
            code.parentNode.insertBefore(image, code);
            image.style = "max-height:none";
            code.style.display = 'none';
        });
    })();
</script>
{{ end }}

<script>
    function highlightzzz() {
        const newNode = document.createElement("style");
        const styleStr = document.createTextNode("mark {background-color: yellow; color: black;}");
        newNode.appendChild(styleStr);
        document.body.appendChild(newNode);

        //保护代码块中的双等号
        let codeBlockList = document.getElementById("main").getElementsByTagName("code");
        let CBList_bk = [];
        for (let i = 0; i < codeBlockList.length; i++) {
            CBList_bk.push(codeBlockList[i].innerHTML);
            codeBlockList[i].innerHTML = i;
        }
        //读取main文本
        let bodyStr = document.getElementById("main").innerHTML;
        //保护非高亮用途的双等号
        bodyStr = bodyStr.replaceAll('"=="', '"$doubleEqualS$"');
        bodyStr = bodyStr.replaceAll("'=='", "'$doubleEqualS$'");

        //替换所有剩下的双等号
        let arr = bodyStr.split("==");
        const cnt = (arr.length - 1) / 2;
        for (let i = 0; i < cnt; i++) {
            bodyStr = bodyStr.replace('==', '<mark>');
            bodyStr = bodyStr.replace('==', '</mark>');
        }

        //替换回非高亮用途的双等号
        bodyStr = bodyStr.replaceAll('"$doubleEqualS$"', '"=="');
        bodyStr = bodyStr.replaceAll("'$doubleEqualS$'", "'=='");
        //将替换后的文本写回main
        document.getElementById("main").innerHTML = bodyStr;
        //替换回代码块中的双等号
        for (let i = 0; i < codeBlockList.length; i++) {
            codeBlockList[i].innerHTML = CBList_bk[i];
        }
    }

    function lineHight() {
        const preTagList = document.getElementsByTagName("pre");
        for (let i = 0; i < preTagList.length; i++) {
            //默认每个pre只有一个code
            let codeBlock = preTagList[i].getElementsByTagName("code")[0];
            let spanList = codeBlock.childNodes;
            for (let j = 0; j < spanList.length; j++) {
                if ("style" in spanList[j]) {   //style中一定有display？
                    if (spanList[j].style["display"] == "flex")
                        spanList[j].style["display"] = "inline";
                    // codeBlock.childNodes[j].style = "margin-top: 0px;"
                    // spanList[j].style = "display: inline;";}
                }
            }
        }
    }
    highlightzzz();
    lineHight();
</script>
```
