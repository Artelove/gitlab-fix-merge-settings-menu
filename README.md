## Проблема:

### Меню открытия\\скрытия измененных файлов и опции просмотра мр-а находятся закрепленными сверху страницы.

![image](https://github.com/Artelove/gitlab-fix-merge-settings-menu/assets/66765809/a1c2a365-09a4-4d09-b302-d08a66162eaf)


### При скролинге по мр\`у это строка так и остается сверху из-за чего возникают неудобства:

1. Открытие иерархии файлов. Чтобы переключиться на файл, при закрытом дереве, нужно **подняться наверх**, **открыть панель файлов** **открыть необходимый файл**,

    > иногда после просмотра нужно **закрыть окно иеррархии файлов** ввиду малого места на экране для комфортного просмотра мр\`а.

    это **долго и неудобно**.

2. В некоторые моменты строки в коде выглядят странно ввиду их большой ширины.
   Пример: ![image](https://github.com/Artelove/gitlab-fix-merge-settings-menu/assets/66765809/42ce7dfa-caec-4774-97ca-4281a31bb293)


    Чтобы посмотреть строку в исходном виде, нужно переключить в настройках просмотра на режим Inline.

    ![Pasted image 20240130101645](https://github.com/Artelove/gitlab-fix-merge-settings-menu/assets/66765809/434b7cb4-6ff3-41f7-8b72-462ad0d7af97)

    Теперь показывается с понятными отступами:

    ![image](https://github.com/Artelove/gitlab-fix-merge-settings-menu/assets/66765809/37ac5e3f-8775-4f5f-bf8e-1af1e3d2d39a)

Однако эта функция так же **закреплена в меню сверху**. И для этого нужно подняться вверх, активировать, и спуститься на просматриваемое место. Это **неудобно**.

## Решение:

### Закрепить по умолчанию меню настроек сверху независимо от скрола.
Пример:

![image](https://github.com/Artelove/gitlab-fix-merge-settings-menu/assets/66765809/61c3d639-9d8a-4fcb-93bb-70ac60b7aa6b)

Благодаря этому появляется возможность иметь **быстрый доступ к настройкам** просматриваемоего пространства.

## Инструкция по установке:

1.  Установить расширение для позволяющее добавлять `js-код` на страницу. [User JavaScript and CSS](https://chromewebstore.google.com/detail/nbhcbdghjpllgmfilhnhkllmkecfmpld) (Или иное расширение поддерживающее внедрение `js` на страницу, работающее в вашем браузере.) (Проверено на **Chromium-платформе** _[Yandex Brouser, Google Chrome, Microsoft Edge, Opera]_ **Для Firefox** [Custom-style-script](https://addons.mozilla.org/en-US/firefox/addon/custom-style-script/))
2.  Ввести необходимый url сайта. `https://git.itl.team/`
    ![Pasted image 20240130124946](https://github.com/Artelove/gitlab-fix-merge-settings-menu/assets/66765809/eafbd02b-d80b-48a9-843c-40e82893b23c)

3.  В поле кода `js` вставить следующий код.

**Возможность настройки**:

> `rowCountFromComment` (строка 1). _Количество строк, которые будут видны после переключения к треду (комменту). (по стрелочкам)_
>
> ![image](https://github.com/Artelove/gitlab-fix-merge-settings-menu/assets/66765809/e140b80c-0b8f-4886-8429-97e54b2bccbc)
>
> По-умолчанию 6
>
> ![image](https://github.com/Artelove/gitlab-fix-merge-settings-menu/assets/66765809/a0a1dbae-0c8e-4fa6-b222-3ed3b748feb6)

```js
const rowCountFromComment = 6; // *Количество строк (>0), которые будут видны после переключения к треду (комменту). (по стрелочкам)*


setTimeout(() => findContainer(), 200);
setTimeout(() => changeScriptsToLocal(), 500);
setTimeout(() => addMarginToTreeSearch(), 500);

function findContainer() {
    let container = document.querySelector(".merge-request-tabs-container");
    let settings = document.querySelector(".mr-version-menus-container");
    if (container == null || settings == null) {
        setTimeout(() => findContainer(), 500);
    } else {
        let styles = "display: flex; flex-direction: row; justify-content: space-between;";
        let block1 = document.createElement("div");
        block1.id = "git_fix_block1";
        block1.style = styles;
        let block2 = document.createElement("div");
        block2.id = "git_fix_block2";
        block2.style = styles;
        container.style = "flex-direction:column";
        container.appendChild(block1);
        container.appendChild(block2);
        settings.style = "width:100%; display:flex; flex-direction: row; align-items: center; line-height: 10px;";
        let merge_request_tabs = document.querySelector(".merge-request-tabs");
        block1.appendChild(settings);
        block2.appendChild(merge_request_tabs);
        changeFileHeadersTopMargin();
        findCounter();
    }
}

function findCounter() {
    let counter = document.querySelector("#discussionCounter");
    if (counter == null) {
        setTimeout(() => findCounter(), 300);
    } else {
        let block2 = document.querySelector("#git_fix_block2");
        block2.appendChild(counter.parentElement);
    }
}

function changeFileHeadersTopMargin() {
    let fileHeaders = document.querySelectorAll("div[data-qa-selector=file_title_container]");
    fileHeaders.forEach((header) => {
        header.style = "--initial-top: calc(var(--header-height, 92px) + 48px);";
    });
    setTimeout(() => changeFileHeadersTopMargin(), 500);
}

function changeScriptsToLocal() {
    let scripts = document.querySelectorAll("script");
    let main = null;
    let pages_merge = null;

    if(document.location.href.includes('/merge_requests/')){
	    scripts.forEach((script) => {
	        if (script.src.includes("/assets/webpack/main"))
	            main = script;
	        if (script.src.includes("/assets/webpack/pages.projects.merge_requests"))
	            pages_merge = script;
	    });
	}

    if (main == null || pages_merge == null) {
        setTimeout(() => changeScriptsToLocal(), 500);
    }

    if (main != null) {
    	console.log(main);
        main.remove();
        addScriptTextContent(main, "main");
    }

    if (pages_merge != null) {
    	console.log(pages_merge);
    	pages_merge.remove();
        addScriptTextContent(pages_merge, "pages_merge");
    }
}

let threadArea = rowCountFromComment * 20 + 46;

function addScriptTextContent(script, type) {
    let text = null;
    var xhr = new XMLHttpRequest();
    xhr.open("GET", script.src)
    xhr.onreadystatechange = function() {
        if (xhr.readyState === XMLHttpRequest.DONE && xhr.status === 200) {
            text = xhr.responseText;
            let outputString = null;

            if (type == "pages_merge") {
                let pattern = /Math\.ceil\(.\)-/gm;
                outputString = text.replace(pattern, (match, name) => `Math.ceil(${text[name+10]}-${threadArea})-`);
            } else if (type == "main") {
                let pattern = /const\{duration:.=.*,offset:.=./
                outputString = text.replace(pattern, `const{duration:t=200,offset:r=-${threadArea}`);
            }

            var ref = document.getElementsByTagName('script')[0],
		script = document.createElement('script');
            script.setAttribute('type', 'text/javascript');
            script.setAttribute('id', `${type.toString()}`);
            script.textContent = outputString;
            ref.parentNode.insertBefore(script, ref);
        }
    };
    xhr.send();
}

function addMarginToTreeSearch(){
	let diffTree = document.querySelector(".diff-tree-list");
	if(diffTree != null){	
		diffTree.style.cssText += "--initial-pos: calc(var(--header-height, 48px) + 48px + 48px)!important;"
	}
	setTimeout(() => addMarginToTreeSearch(), 500);
}

```

4. Сохранить правило.
   ![Pasted image 20240130125240](https://github.com/Artelove/gitlab-fix-merge-settings-menu/assets/66765809/a05a98f8-d000-41e0-8b6a-3bd380ac7752)

5. Полностью обновить страницу **`Ctrl+F5`** с Gitlab и пользоваться результатом.

## Информация:

> Прошу оценить звездой, если эта разработка была вам полезна. <a class="github-button" href="https://github.com/Artelove/gitlab-fix-merge-settings-menu" data-color-scheme="no-preference: light; light: light_high_contrast; dark: light;" data-icon="octicon-star" data-size="large" data-show-count="true" aria-label="Star Artelove/gitlab-fix-merge-settings-menu on GitHub">Star to Artelove</a>

### По вопросам и улучшениям обращаться:

_Александр Карагичев_

[Discord](https://discordapp.com/users/264400114141888512)

[Telegram](https://t.me/arteevil)
