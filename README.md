setTimeout(() => findContainer(), 1000);

async function findContainer() {
	let container = document.querySelector(".merge-request-tabs-container");
	let settings = document.querySelector(".mr-version-menus-container");
	let counter = document.querySelector("#discussionCounter");

	console.log(container);

	if (container == null || settings == null) {
		await setTimeout(() => findContainer(), 1000);
	} else {
		setTimeout(() => changeFileHeadersTopMargin(), 1000);
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
		settings.style = "width:100%; display:flex; flex-direction:row";
		let merge_request_tabs = document.querySelector(".merge-request-tabs");

		block1.appendChild(settings);
		block2.appendChild(merge_request_tabs);
	}
}

async function findCounter() {
	let counter = document.querySelector("#discussionCounter");
	if (counter == null) {
		setTimeout(() => findCounter(), 1000);
	} else {
		let block2 = document.querySelector("#git_fix_block2");
		block2.appendChild(counter.parentElement);
	}
}

async function changeFileHeadersTopMargin(){
	let fileHeaders = document.querySelectorAll("div[data-qa-selector=file_title_container]");
	console.log(fileHeaders);
	fileHeaders.forEach((header) => {
		header.style = "--initial-top: calc(var(--header-height, 92px) + 48px);"
	});
	setTimeout(() => changeFileHeadersTopMargin(), 1000);
}
