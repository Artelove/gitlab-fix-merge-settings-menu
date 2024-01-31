## Проблема:

Меню открытия\\скрытия измененных файлов и опции просмотра мр-а находятся закрепленными сверху страницы.

![](attachments/Pasted%20image%2020240130125828.jpeg)

При скролинге по мр\`у это строка так и остается сверху из-за чего возникают неудобства:

1. Чтобы переключиться на файл, при закрытом дереве, нужно сначала его открыть, а для этого подняться наверх, открыть и после открыть необходимый файл, и после просмотра закрыть окно ввиду малого места на экране для комфортного просмотра мр\`а. (**долго и неудобно**)
2. В некоторые моменты строки в коде выглядят странно ввиду их большой ширины. Пример: ![](attachments/Pasted%20image%2020240130101540.jpeg)

    Чтобы посмотреть строку в исходном виде, нужно переключить в настройках просмотра на режим Inline.

    ![](attachments/Pasted%20image%2020240130101645.jpeg)

    Теперь показывается с понятными отступами:

    ![](attachments/Pasted%20image%2020240130101731.jpeg)

Однако эта функция так же закреплена в меню сверху. И для этого нужно подняться вверх, активировать, и спуститься на просматриваемое место. Это **неудобно**.

## Решение:

Закрепить по умолчанию меню настроек сверху независимо от скрола путем изменения `css`.
Пример:

![](attachments/Pasted%20image%2020240130101540.jpeg)

![](attachments/Pasted%20image%2020240130102048.jpeg)
Благодаря этому появляется возможность быстро изменять просматриваемое пространство.

## Инструкция по установке:

1.  Установить расширение для позволяющее добавлять `js-код` на страницу. [User JavaScript and CSS](https://chromewebstore.google.com/detail/nbhcbdghjpllgmfilhnhkllmkecfmpld) (Или иное расширение поддерживающее внедрение `js` на страницу, работающее в вашем браузере.)
2.  Ввести необходимый url сайта. `https://git.itl.team/`
    ![](Pasted%20image%2020240130124946.png)
3.  В поле кода `js` вставить следующий код.

```js
async function findContainer() {
	let container = document.querySelector(".merge-request-tabs-container");
	let settings = document.querySelector(".mr-version-menus-container");
	let counter = document.querySelector("#discussionCounter");

	console.log(container);

	if (container == null || settings == null || counter == null) {
		await setTimeout(() => findContainer(), 1000);
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

		let merge_request_tabs = document.querySelector(".merge-request-tabs");

		block1.appendChild(settings);
		block2.appendChild(merge_request_tabs);
		block2.appendChild(counter.parentElement);
	}
}
```

4. Сохранить правило.
   ![](attachments/Pasted%20image%2020240130125240.jpeg)
5. Обновить странцу с Gitlab и пользоваться результатом.

По вопросам и улучшениям обращаться: _Александр Карагичев_

[Discord](https://discordapp.com/users/264400114141888512)

[Telegram](https://t.me/arteevil)
