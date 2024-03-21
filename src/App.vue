<script setup lang="ts">
import HelloWorld from './components/HelloWorld.vue'
import TheWelcome from './components/TheWelcome.vue'

import * as marked from 'marked';
import { ref } from 'vue';

const markdown = ref('')

async function markdownToMJML(markdownInput: string) {
	// Convert Markdown to HTML using Marked library

	const htmlOutput: string = (await marked.parse(markdownInput))

	// Convert HTML to MJML
	const mjmlOutput: string = convertHtmlToMjml(htmlOutput);

	rendered.value = mjmlOutput.replace(/&amp;/g, "&").replace(/%7B/g, "{").replace(/%7D/g, "}");
}

function convertHtmlToMjml(htmlInput: string): string {
	// Convert HTML string to DOM object
	const parser = new DOMParser();
	const doc = parser.parseFromString(htmlInput, 'text/html');

	// Iterate over each element in the HTML and convert it to MJML
	const mjmlElements: string[] = [];
	const htmlElements = doc.body.children;
	for (let i = 0; i < htmlElements.length; i++) {
		const mjmlElement = convertHtmlElementToMjml(htmlElements[i]);
		mjmlElements.push(mjmlElement);
	}

	// Join all MJML elements into a single string
	const mjmlOutput: string = mjmlElements.join('\n\n');
	return mjmlOutput;
}
function convertHtmlElementToMjml(element: Element): string {
	switch (element.tagName.toLowerCase()) {
		case 'p':
			// Check if the <p> tag contains only an <img> tag
			if (element.children.length === 1 && element.children[0].tagName.toLowerCase() === 'img') {
				const imgElement = element.children[0] as HTMLImageElement;
				const src = imgElement.getAttribute('src') || '';
				const alt = imgElement.getAttribute('alt') || '';
				return `<mj-image src="${src}" alt="${alt}" />`;
			} else {
				return `<mj-text>${element.innerHTML.trim()}</mj-text>`;
			}
		case 'h1':
			return `<mj-text font-size="24px" font-weight="800">${element.innerHTML.trim()}</mj-text>`;
		case 'h2':
			return `<mj-text font-size="22px" font-weight="700">${element.innerHTML.trim()}</mj-text>`;
		case 'h3':
			return `<mj-text font-size="20px" font-weight="600">${element.innerHTML.trim()}</mj-text>`;
		case 'h4':
			return `<mj-text font-size="18px" font-weight="500">${element.innerHTML.trim()}</mj-text>`;
		case 'h5':
			return `<mj-text font-size="16px" font-weight="400">${element.innerHTML.trim()}</mj-text>`;
		case 'h6':
			return `<mj-text font-size="14px" font-weight="300">${element.innerHTML.trim()}</mj-text>`;
		case 'strong':
			return `<mj-text font-weight="bold">${element.innerHTML.trim()}</mj-text>`;
		case 'em':
			return `<mj-text font-style="italic">${element.innerHTML.trim()}</mj-text>`;
		case 'a':
			const href = element.getAttribute('href') || '';
			return `<mj-text><a href="${href}">${element.innerHTML.trim()}</a></mj-text>`;
		case 'ul':
			return `<mj-text><ul>${element.innerHTML.trim()}</ul></mj-text>`;
		case 'ol':
			return `<mj-text><ol>${element.innerHTML.trim()}</ol></mj-text>`;
		case 'li':
			return `<mj-text><li>${element.innerHTML.trim()}</li></mj-text>`;
		case 'blockquote':
			return `<mj-text font-style="italic">${element.innerHTML.trim()}</mj-text>`;
		case 'code':
			return `<mj-raw>${element.innerHTML.trim()}</mj-raw>`;
		case 'pre':
			return `<mj-raw>${element.innerHTML.trim()}</mj-raw>`;
		// Add more cases for other supported HTML elements
		default:
			return '';
	}
}




const rendered = ref('')
</script>

<template>
	<div class="w-screen h-screen flex flex-col bg-slate-100 p-8 gap-y-8">
		<div>
			<h1 class="font-bold text-2xl">Markdown to MJML Converter</h1>
			<p>Made by <a class="text-green-700 font-medium" href="https://twitter.com/WillRBishop">@WillRBishop</a></p>
		</div>
		<div class="flex gap-x-8 overflow-scroll w-full h-full">
			<textarea v-model="markdown" class="w-1/2" @input="markdownToMJML(markdown)" />
			<textarea disabled class="w-1/2  bg-white font-mono whitespace-pre-line overflow-scroll">{{ rendered }}</textarea>
		</div>
	</div>
</template>

<style scoped>
header {
	line-height: 1.5;
}

.logo {
	display: block;
	margin: 0 auto 2rem;
}

@media (min-width: 1024px) {
	header {
		display: flex;
		place-items: center;
		padding-right: calc(var(--section-gap) / 2);
	}

	.logo {
		margin: 0 2rem 0 0;
	}

	header .wrapper {
		display: flex;
		place-items: flex-start;
		flex-wrap: wrap;
	}
}
</style>
