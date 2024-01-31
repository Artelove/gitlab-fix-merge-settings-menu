## Проблема:

Меню открытия\\скрытия измененных файлов и опции просмотра мр-а находятся закрепленными сверху страницы.

![Pasted image 20240130125828](https://github.com/Artelove/gitlab-fix-merge-settings-menu/assets/66765809/4bfd6e19-c8e6-42f6-a309-7cf7e0daf7a3)

При скролинге по мр\`у это строка так и остается сверху из-за чего возникают неудобства:

1. Чтобы переключиться на файл, при закрытом дереве, нужно сначала его **открыть дерево**, а для этого **подняться наверх**, **открыть панель файлов** **открыть необходимый файл**, и после просмотра нужно **закрыть окно иеррархии файлов** ввиду малого места на экране для комфортного просмотра мр\`а. (**долго и неудобно**)
2. В некоторые моменты строки в коде выглядят странно ввиду их большой ширины.
    Пример: ![Pasted image 20240130101540](https://github.com/Artelove/gitlab-fix-merge-settings-menu/assets/66765809/0c492736-3917-4eb1-b524-55392fdc6cee)


    Чтобы посмотреть строку в исходном виде, нужно переключить в настройках просмотра на режим Inline.

   ![Pasted image 20240130101645](https://github.com/Artelove/gitlab-fix-merge-settings-menu/assets/66765809/434b7cb4-6ff3-41f7-8b72-462ad0d7af97)


    Теперь показывается с понятными отступами:

    ![Pasted image 20240130101731](https://github.com/Artelove/gitlab-fix-merge-settings-menu/assets/66765809/fa18b6ad-ed2c-411e-b6f5-814ddd3a7656)


Однако эта функция так же закреплена в меню сверху. И для этого нужно подняться вверх, активировать, и спуститься на просматриваемое место. Это **неудобно**.

## Решение:

Закрепить по умолчанию меню настроек сверху независимо от скрола.
Пример:

![image](https://github.com/Artelove/gitlab-fix-merge-settings-menu/assets/66765809/444a33aa-0ab6-465d-963d-2f82545f661e)



Благодаря этому появляется возможность быстро изменять просматриваемое пространство.

## Инструкция по установке:

1.  Установить расширение для позволяющее добавлять `js-код` на страницу. [User JavaScript and CSS](https://chromewebstore.google.com/detail/nbhcbdghjpllgmfilhnhkllmkecfmpld) (Или иное расширение поддерживающее внедрение `js` на страницу, работающее в вашем браузере.)
2.  Ввести необходимый url сайта. `https://git.itl.team/`
    ![Pasted image 20240130124946](https://github.com/Artelove/gitlab-fix-merge-settings-menu/assets/66765809/eafbd02b-d80b-48a9-843c-40e82893b23c)

3.  В поле кода `js` вставить следующий код.

```js
setTimeout(() => findContainer(), 1000);


async function findContainer() {
	let container = document.querySelector(".merge-request-tabs-container");
	let settings = document.querySelector(".mr-version-menus-container");
	let counter = document.querySelector("#discussionCounter");

	console.log(container);

	if (container == null || settings == null) {
		await setTimeout(() => findContainer(), 1000);
	} else {
		setTimeout(() => findCounter(), 1000);
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
		settings.style = "width:100%";
		let merge_request_tabs = document.querySelector(".merge-request-tabs");

		block1.appendChild(settings);
		block2.appendChild(merge_request_tabs);
	}
}

async function findCounter(){
	let counter = document.querySelector("#discussionCounter");
	let block2 = document.querySelector("#git_fix_block2");
	block2.appendChild(counter.parentElement);
}
```

4. Сохранить правило.
 ![Pasted image 20240130125240](https://github.com/Artelove/gitlab-fix-merge-settings-menu/assets/66765809/a05a98f8-d000-41e0-8b6a-3bd380ac7752)

5. Обновить странцу с Gitlab и пользоваться результатом.

По вопросам и улучшениям обращаться: 

_Александр Карагичев_

[Discord](https://discordapp.com/users/264400114141888512)

[Telegram](https://t.me/arteevil)
