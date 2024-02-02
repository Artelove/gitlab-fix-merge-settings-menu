## Проблема:

Меню открытия\\скрытия измененных файлов и опции просмотра мр-а находятся закрепленными сверху страницы.

![Pasted image 20240130125828](https://github.com/Artelove/gitlab-fix-merge-settings-menu/assets/66765809/4bfd6e19-c8e6-42f6-a309-7cf7e0daf7a3)

При скролинге по мр\`у это строка так и остается сверху из-за чего возникают неудобства:

1. Открытие иерархии файлов. Чтобы переключиться на файл, при закрытом дереве, нужно **подняться наверх**, **открыть панель файлов** **открыть необходимый файл**,
   > иногда после просмотра нужно **закрыть окно иеррархии файлов** ввиду малого места на экране для комфортного просмотра мр\`а.
   
   это **долго и неудобно**.
2. В некоторые моменты строки в коде выглядят странно ввиду их большой ширины.
   Пример: ![Pasted image 20240130101540](https://github.com/Artelove/gitlab-fix-merge-settings-menu/assets/66765809/0c492736-3917-4eb1-b524-55392fdc6cee)

    Чтобы посмотреть строку в исходном виде, нужно переключить в настройках просмотра на режим Inline.

    ![Pasted image 20240130101645](https://github.com/Artelove/gitlab-fix-merge-settings-menu/assets/66765809/434b7cb4-6ff3-41f7-8b72-462ad0d7af97)

    Теперь показывается с понятными отступами:

    ![Pasted image 20240130101731](https://github.com/Artelove/gitlab-fix-merge-settings-menu/assets/66765809/fa18b6ad-ed2c-411e-b6f5-814ddd3a7656)

Однако эта функция так же **закреплена в меню сверху**. И для этого нужно подняться вверх, активировать, и спуститься на просматриваемое место. Это **неудобно**.

## Решение:

Закрепить по умолчанию меню настроек сверху независимо от скрола.
Пример:

![image](https://github.com/Artelove/gitlab-fix-merge-settings-menu/assets/66765809/61c3d639-9d8a-4fcb-93bb-70ac60b7aa6b)

Благодаря этому появляется возможность иметь **быстрый доступ к настройкам** просматриваемоего пространства.

## Инструкция по установке:

1.  Установить расширение для позволяющее добавлять `js-код` на страницу. [User JavaScript and CSS](https://chromewebstore.google.com/detail/nbhcbdghjpllgmfilhnhkllmkecfmpld) (Или иное расширение поддерживающее внедрение `js` на страницу, работающее в вашем браузере.) (Проверено на Chromium-платформе _[Yandex Brouser, Google Chrome, Microsoft Edge, Opera]_ )
2.  Ввести необходимый url сайта. `https://git.itl.team/`
    ![Pasted image 20240130124946](https://github.com/Artelove/gitlab-fix-merge-settings-menu/assets/66765809/eafbd02b-d80b-48a9-843c-40e82893b23c)

3.  В поле кода `js` вставить следующий код.

```js
setTimeout(() => findContainer(), 500);

async function findContainer() {
	let container = document.querySelector(".merge-request-tabs-container");
	let settings = document.querySelector(".mr-version-menus-container");

	if (container == null || settings == null) {
		await setTimeout(() => findContainer(), 500);
	} else {
		setTimeout(() => changeFileHeadersTopMargin(), 100);
		setTimeout(() => findCounter(), 100);
		setTimeout(() => addListenersForTabs(), 100);
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
	}
}

async function findCounter() {
	let counter = document.querySelector("#discussionCounter");
	if (counter == null) {
		setTimeout(() => findCounter(), 500);
	} else {
		setTimeout(() => setAdditionalScrollToSwitchCommentsButton(), 100);
		let block2 = document.querySelector("#git_fix_block2");
		block2.appendChild(counter.parentElement);
	}
}

async function changeFileHeadersTopMargin(){
	let fileHeaders = document.querySelectorAll("div[data-qa-selector=file_title_container]");
	fileHeaders.forEach((header) => {
		header.style = "--initial-top: calc(var(--header-height, 92px) + 48px);"
	});
	setTimeout(() => changeFileHeadersTopMargin(), 1000);
}

async function addListenersForTabs(){
	document.addEventListener("mousedown", (e) => {
			var el = document.elementFromPoint(e.clientX, e.clientY);
			let tabs = document.querySelector("li[data-qa-selector='diffs_tab'").parentNode;
			if(isDescendant(tabs,el)){
				el.addEventListener("click", (qw)=>console.log("dispatchEvent click"));
				el.dispatchEvent(new Event("click"));
				hideOrShowBlock();
			}
	});
}

function hideOrShowBlock(){
	let diffs = document.querySelector("li[data-qa-selector='diffs_tab'");
	let block1 = document.querySelector("#git_fix_block1");
		if(diffs.classList.contains("active")){
				block1.style.display = "flex";
		}
		else{
				block1.style.display = "none";
		}
}

function isDescendant(parent, child) {
     var node = child.parentNode;
     while (node != null) {
         if (node == parent) {
             return true;
         }
         node = node.parentNode;
     }
     return false;
}

function setAdditionalScrollToSwitchCommentsButton(){
	let mr_down_button = document.querySelector("button[data-track-label=mr_next_unresolved_thread]");
	let mr_up_button = document.querySelector("button[data-track-label=mr_previous_unresolved_thread]");
	mr_down_button.addEventListener("mouseup", (e)=>{
		window.scroll(0,window.pageYOffset+147);
		setTimeout(()=>document.addEventListener("scrollend", myScroll),200);
	});
	
	mr_up_button.addEventListener("mouseup", (e)=>{
		window.scroll(0,window.pageYOffset-147);
		setTimeout(()=>document.addEventListener("scrollend", myScroll),200);
	});

}

function myScroll() {
	window.scrollBy({	
			top: -147,
			left: 0,
			behavior: "smooth"});
	document.removeEventListener("scrollend",myScroll);
}

```

4. Сохранить правило.
   ![Pasted image 20240130125240](https://github.com/Artelove/gitlab-fix-merge-settings-menu/assets/66765809/a05a98f8-d000-41e0-8b6a-3bd380ac7752)

5. Обновить странцу с Gitlab и пользоваться результатом.

## Информация:

> Прошу оценить звездой, если эта разработка была вам полезна. <a class="github-button" href="https://github.com/Artelove/gitlab-fix-merge-settings-menu" data-color-scheme="no-preference: light; light: light_high_contrast; dark: light;" data-icon="octicon-star" data-size="large" data-show-count="true" aria-label="Star Artelove/gitlab-fix-merge-settings-menu on GitHub">Star to Artelove</a>

### По вопросам и улучшениям обращаться:

_Александр Карагичев_

[Discord](https://discordapp.com/users/264400114141888512)

[Telegram](https://t.me/arteevil)
