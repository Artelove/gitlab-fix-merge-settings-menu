## Проблема:

Меню открытия\\скрытия измененных файлов и опции просмотра мр-а находятся закрепленными сверху страницы.

![Pasted image 20240130125828](https://github.com/Artelove/gitlab-fix-merge-settings-menu/assets/66765809/4bfd6e19-c8e6-42f6-a309-7cf7e0daf7a3)

При скролинге по мр\`у это строка так и остается сверху из-за чего возникают неудобства:

1. Открытие иерархии файлов. Чтобы переключиться на файл, при закрытом дереве, нужно **подняться наверх**, **открыть панель файлов** **открыть необходимый файл**,

    > иногда после просмотра нужно **закрыть окно иеррархии файлов** ввиду малого места на экране для комфортного просмотра мр\`а.

    это **долго и неудобно**.

2. В некоторые моменты строки в коде выглядят странно ввиду их большой ширины.
   Пример: ![image](https://github.com/Artelove/gitlab-fix-merge-settings-menu/assets/66765809/6afc4cfd-379a-4f10-b214-a31a5290f79e)

    Чтобы посмотреть строку в исходном виде, нужно переключить в настройках просмотра на режим Inline.

    ![Pasted image 20240130101645](https://github.com/Artelove/gitlab-fix-merge-settings-menu/assets/66765809/434b7cb4-6ff3-41f7-8b72-462ad0d7af97)

    Теперь показывается с понятными отступами:

    ![image](https://github.com/Artelove/gitlab-fix-merge-settings-menu/assets/66765809/d9f39e5d-7334-42f1-ac05-52415d4e8b0a)

Однако эта функция так же **закреплена в меню сверху**. И для этого нужно подняться вверх, активировать, и спуститься на просматриваемое место. Это **неудобно**.

## Решение:

Закрепить по умолчанию меню настроек сверху независимо от скрола.
Пример:

![image](https://github.com/Artelove/gitlab-fix-merge-settings-menu/assets/66765809/61c3d639-9d8a-4fcb-93bb-70ac60b7aa6b)

Благодаря этому появляется возможность иметь **быстрый доступ к настройкам** просматриваемоего пространства.

## Инструкция по установке:

1.  Установить расширение для позволяющее добавлять `js-код` на страницу. [User JavaScript and CSS](https://chromewebstore.google.com/detail/nbhcbdghjpllgmfilhnhkllmkecfmpld) (Или иное расширение поддерживающее внедрение `js` на страницу, работающее в вашем браузере.) (Проверено на **Chromium-платформе** _[Yandex Brouser, Google Chrome, Microsoft Edge, Opera]_ **Для Firefox** [Custom-style-script](https://addons.mozilla.org/en-US/firefox/addon/custom-style-script/))
2.  Ввести необходимый url сайта. `https://git.itl.team/`
    ![Pasted image 20240130124946](https://github.com/Artelove/gitlab-fix-merge-settings-menu/assets/66765809/eafbd02b-d80b-48a9-843c-40e82893b23c)

3.  В поле кода `js` вставить следующий код.

**Возможность настройки**:

> `scrollRowCountFromComment` (строка 1). _Количество строк, которые будут видны после переключения к треду (комменту). (по стрелочкам)_
>
> ![image](https://github.com/Artelove/gitlab-fix-merge-settings-menu/assets/66765809/e140b80c-0b8f-4886-8429-97e54b2bccbc)
>
> По-умолчанию 6
>
> ![image](https://github.com/Artelove/gitlab-fix-merge-settings-menu/assets/66765809/a0a1dbae-0c8e-4fa6-b222-3ed3b748feb6)

```js
const scrollRowCountFromComment = 6; // *Количество строк (>0), которые будут видны после переключения к треду (комменту). (по стрелочкам)*

setTimeout(() => findContainer(), 200);
async function findContainer() {
	let container = document.querySelector(".merge-request-tabs-container");
	let settings = document.querySelector(".mr-version-menus-container");
	if (container == null || settings == null) {
		await setTimeout(() => findContainer(), 500);
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
		addListenersForTabs();
	}
}
async function findCounter() {
	let counter = document.querySelector("#discussionCounter");
	if (counter == null) {
		setTimeout(() => findCounter(), 300);
	} else {
		let block2 = document.querySelector("#git_fix_block2");
		block2.appendChild(counter.parentElement);
		waitResetConter();
	}
}
async function changeFileHeadersTopMargin() {
	let fileHeaders = document.querySelectorAll("div[data-qa-selector=file_title_container]");
	fileHeaders.forEach((header) => {
		header.style = "--initial-top: calc(var(--header-height, 92px) + 48px);";
	});
	setTimeout(() => changeFileHeadersTopMargin(), 500);
}
async function addListenersForTabs() {
	document.addEventListener("mousedown", (e) => {
		var el = document.elementFromPoint(e.clientX, e.clientY);
		let tabs = document.querySelector("li[data-qa-selector='diffs_tab'").parentNode;
		if (isDescendant(tabs, el)) {
			el.dispatchEvent(new Event("click"));
			hideOrShowBlock();
		}
	});
}
async function hideOrShowBlock() {
	let diffs = document.querySelector("li[data-qa-selector='diffs_tab'");
	let block1 = document.querySelector("#git_fix_block1");
	let mr_down_button = document.querySelector("button[data-track-label=gitlab_mr_next_unresolved_thread]");
	let mr_up_button = document.querySelector("button[data-track-label=gitlab_mr_previous_unresolved_thread]");
	let clone_mr_down_button = document.querySelector("button[data-track-label=clone_mr_next_unresolved_thread]");
	let clone_mr_up_button = document.querySelector("button[data-track-label=clone_mr_previous_unresolved_thread]");
	try {
		if (diffs.classList.contains("active")) {
			mr_down_button.style.display = "none";
			mr_up_button.style.display = "none";
			clone_mr_down_button.style.display = "inline-flex";
			clone_mr_up_button.style.display = "inline-flex";
		} else {
			clone_mr_down_button.style.display = "none";
			clone_mr_up_button.style.display = "none";
			mr_down_button.style.display = "inline-flex";
			mr_up_button.style.display = "inline-flex";
		}
	} catch (e) {
		console.log(e);
	}
	setTimeout(() => hideOrShowBlock(), 200);
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
async function waitResetConter() {
	let mr_down_button = document.querySelector("button[data-track-label=mr_next_unresolved_thread]");
	let mr_up_button = document.querySelector("button[data-track-label=mr_previous_unresolved_thread]");
	if (mr_down_button == null) {
		setTimeout(waitResetConter, 200);
	} else {
		document.addEventListener("scroll", replaceOriginalButtonsSwitchComments, {
			once: true,
		});
		setTimeout(() => mr_down_button.dispatchEvent(new Event("click")), 200);
		setTimeout(waitResetConter, 1000);
	}
}
let is_enable = true;
let is_my_scrolling = true;
async function replaceOriginalButtonsSwitchComments(e) {
	window.scroll(window.scrollX, window.scrollY);
	let mr_down_button = document.querySelector("button[data-track-label=mr_next_unresolved_thread]");
	let mr_up_button = document.querySelector("button[data-track-label=mr_previous_unresolved_thread]");
	let parent = mr_down_button.parentElement;
	let clone_mr_down_button = mr_down_button.cloneNode(true);
	let clone_mr_up_button = mr_up_button.cloneNode(true);
	clone_mr_down_button.setAttribute("data-track-label", "clone_mr_next_unresolved_thread");
	clone_mr_up_button.setAttribute("data-track-label", "clone_mr_previous_unresolved_thread");
	mr_down_button.setAttribute("data-track-label", "gitlab_mr_next_unresolved_thread");
	mr_up_button.setAttribute("data-track-label", "gitlab_mr_previous_unresolved_thread");
	mr_down_button.style.display = "none";
	mr_up_button.style.display = "none";
	parent.insertBefore(clone_mr_down_button, parent.firstChild);
	parent.insertBefore(clone_mr_up_button, parent.firstChild);
	clone_mr_down_button.addEventListener("click", (e) => {
		doMyScroll();
	});
	clone_mr_up_button.addEventListener("click", (e) => {
		doMyScroll(false);
	});
	document.addEventListener("wheel", (event) => {
		console.log(event);
		currentElementFocus = null;
	});
	document.addEventListener("mousedown", (event) => {
		if (event && event.button == 4) {
			console.log("middleclicked");
			console.log(event);
			currentElementFocus = null;
		}
	});
}
let currentElementFocus = null;
async function doMyScroll(is_down = true) {
	let diffs = document.querySelectorAll("div.diff-grid-comments ul.notes div.note-actions button.line-resolve-btn");
	let notResolvedDiffs = [];
	diffs.forEach((diff) => {
		if (diff.classList.contains("is-active")) return;
		notResolvedDiffs.push(diff);
	});
	let currentPositionY = window.scrollY;
	let nearestElement = null;
	let minOffest = 99999999;
	let offset = null;
	try {
		if (document.body.contains(currentElementFocus) == false || notResolvedDiffs.indexOf(currentElementFocus) == -1) {
			currentElementFocus = null;
		}
		if (currentElementFocus == null) {
			notResolvedDiffs.forEach((diff) => {
				offset = getPositionTopOfElement(diff) - currentPositionY -45 - scrollRowCountFromComment * 20;
				if (is_down) {
					if (offset > 0) {
						nearestElement = diff;
						throw new Error("diff founded");
					}
				} else {
					if (offset < 0) {
						nearestElement = diff;
						minOffest = offset;
					}
					if (offset > 0) {
						throw new Error("diff founded");
					}
				}
			});
		} else {
			let index = notResolvedDiffs.indexOf(currentElementFocus);
			if (is_down) {
				if (index < notResolvedDiffs.length) {
					nearestElement = notResolvedDiffs.at(index + 1);
				}
			} else {
				if (index >= 0) {
					nearestElement = notResolvedDiffs.at(index - 1);
				}
			}
		}
	} catch (e) {
		if (e.message !== "diff founded") {
			throw e;
		}
	}
	if (nearestElement == null) {
		if (is_down) {
			nearestElement = notResolvedDiffs.at(0);
		} else {
			nearestElement = notResolvedDiffs.at(notResolvedDiffs.length - 1);
		}
	}
	currentElementFocus = nearestElement;
	let diffBody = getDiffBody(currentElementFocus);
	let elementOffset = getPositionTopOfElement(currentElementFocus);
	
	if(diffBody != null)
		elementOffset = getPositionTopOfElement(diffBody);
		
	window.scroll({
		top: window.scrollY + (elementOffset - window.scrollY) - 37 - scrollRowCountFromComment * 20,
		left: window.scrollX,
		behavior: "smooth",
	});
	console.log("doMyScroll");
}

function getPositionTopOfElement(element) {
	var bodyRect = document.body.getBoundingClientRect();
	elemRect = element.getBoundingClientRect();
	return elemRect.top - bodyRect.top - 100;
}

function getDiffBody(elem){
	let diffs = document.querySelectorAll("div[data-qa-selector='discussion_content']");
	let diffBody = null;
	diffs.forEach((diff)=>{
		if(diff.contains(elem)){
			diffBody = diff;
		}
	});
	return diffBody;
}
console.log("gitlab fix setting added");
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
