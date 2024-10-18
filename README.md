# trix-svelte
Svelte Wrappers for trix


If you want this to work with prerendering, add this to your vite.config.ts:

```
ssr: {
		noExternal: ['@ernestasthedev/trix']
	},
```

Need little bit of CSS in your app.cs file, unless you do your own toolbar.

```
@layer components {
  .trix-button {
    @apply py-1.5 px-2 bg-gray-200 hover:bg-gray-300 border-gray-300;
  }

  .trix-button.trix-active {
    @apply bg-gray-500 border-gray-600 text-white;
  }

  .basic-list ul {
    list-style-type: disc;
    margin-left: 1rem;
  }

  .basic-list ol {
    list-style-type: decimal;
    margin-left: 1rem;
  }
}
```

The .basic-list is needed because tailwind strips all default styles so we need to re-add them. If you use tailwind typography, you could use .prose class instead to get all default stuff back.


# editor-toolbar.svelte:

Depends on Skeleton from shadcn

I have the 'hideBullets' which hides the list buttons from the toolbar, feel free to remove it if not needed

```
<script lang="ts">
	import { browser } from '$app/environment';
	import { cn } from '$lib/utils.js';
	import { Skeleton } from '$lib/components/ui/skeleton';
	import TrixStarter from '@ernestasthedev/trix';

	let {
		id,
		hideBullets,
		class: classname
	}: {
		id: string;
		hideBullets?: boolean;
		class?: string;
	} = $props();

	function setupTrixToolbar() {
		return `<div class="row flex">
      <span class="flex" data-trix-button-group="text-tools">
        <button type="button" class="trix-button  rounded-xl rounded-r-none border-r" data-trix-attribute="bold" data-trix-key="b" title="Bold" tabindex="-1">
          <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
            <path d="M6 4h8a4 4 0 0 1 4 4 4 4 0 0 1-4 4H6z"></path>
            <path d="M6 12h9a4 4 0 0 1 4 4 4 4 0 0 1-4 4H6z"></path>
          </svg>
        </button>
        <button type="button" class="trix-button rounded-none border-x" data-trix-attribute="italic" data-trix-key="i" title="Italic" tabindex="-1">
          <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
            <line x1="19" y1="4" x2="10" y2="4"></line>
            <line x1="14" y1="20" x2="5" y2="20"></line>
            <line x1="15" y1="4" x2="9" y2="20"></line>
          </svg>
        </button>
        <button type="button" class="trix-button rounded-none border-x" data-trix-attribute="strike" title="Strike" tabindex="-1">
          <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
            <line x1="18" y1="12" x2="6" y2="12"></line>
            <line x1="18" y1="6" x2="6" y2="18"></line>
          </svg>
        </button>
        <button type="button" class="trix-button border-l rounded-xl rounded-l-none" data-trix-attribute="href" data-trix-action="link" data-trix-key="k" title="Link" tabindex="-1">
          <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
            <path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path>
            <path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path>
          </svg>
        </button>
      </span>

	<span class="flex ml-2 [.no-bullets_&]:hidden" data-trix-button-group="text-tools">
        <button type="button" class="trix-button border-r rounded-xl rounded-r-none" data-trix-attribute="bullet" title="Bulleted List" tabindex="-1">
          <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
            <line x1="8" y1="6" x2="21" y2="6"></line>
            <line x1="8" y1="12" x2="21" y2="12"></line>
            <line x1="8" y1="18" x2="21" y2="18"></line>
            <line x1="3" y1="6" x2="3.01" y2="6"></line>
            <line x1="3" y1="12" x2="3.01" y2="12"></line>
            <line x1="3" y1="18" x2="3.01" y2="18"></line>
          </svg>
        </button>
        <button type="button" class="trix-button border-l rounded-xl rounded-l-none" data-trix-attribute="number" title="Numbered List" tabindex="-1">
          <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
            <line x1="10" y1="6" x2="21" y2="6"></line>
            <line x1="10" y1="12" x2="21" y2="12"></line>
            <line x1="10" y1="18" x2="21" y2="18"></line>
            <path d="M4 6h1v4"></path>
            <path d="M4 10h2"></path>
            <path d="M6 18H4c0-1 2-2 2-3s-1-1.5-2-1"></path>
          </svg>
        </button>
      </span>

      <span class="flex-grow"></span>

      <span class="flex" data-trix-button-group="history-tools">
        <button type="button" class="trix-button border-r rounded-xl rounded-r-none" data-trix-action="undo" data-trix-key="z" title="Undo" tabindex="-1">
          <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
            <path d="M3 7v6h6"></path>
            <path d="M21 17a9 9 0 00-9-9 9 9 0 00-6 2.3L3 13"></path>
          </svg>
        </button>
        <button type="button" class="trix-button rounded-xl rounded-l-none" data-trix-action="redo" data-trix-key="shift+z" title="Redo" tabindex="-1">
          <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
            <path d="M21 7v6h-6"></path>
            <path d="M3 17a9 9 0 019-9 9 9 0 016 2.3l3 2.7"></path>
          </svg>
        </button>
      </span>
    </div>

    <div class="trix-dialogs" data-trix-dialogs>
      <div class="trix-dialog trix-dialog--link" data-trix-dialog="href" data-trix-dialog-attribute="href">
        <div class="flex flex-col space-y-2 p-2">
          <input type="url" name="href" class="border rounded p-1 text-sm" placeholder="URL or email" aria-label="URL" data-trix-input>
          <div class="flex space-x-2">
            <button type="button" class="trix-button rounded-xl text-sm" data-trix-method="setAttribute">Link</button>
            <button type="button" class="trix-button rounded-xl text-sm"data-trix-method="removeAttribute">Unlink</button>
          </div>
        </div>
      </div>
    </div>`;
	}

	$effect.pre(() => {
		document.addEventListener('trix-before-initialize', () => {
			(window as any).Trix.config.toolbar.getDefaultHTML = setupTrixToolbar;
		});
	});

	let loaded = $state(false);
</script>

{#if !loaded}
	<div class="row flex">
		<Skeleton class={cn(`h-7 w-[8.4rem] rounded-xl`, classname)} />
		<Skeleton class={cn(`ml-2 h-7 w-[4.2rem] rounded-xl`, classname)} />
		<span class="flex-grow"></span>
		<Skeleton class={cn(`ml-2 h-7 w-[4.2rem] rounded-xl`, classname)} />
	</div>
{/if}
{#if browser}
	{#await (async () => {
		await TrixStarter.init();
		loaded = true;
	})() then}
		<trix-toolbar {id} class={cn(`w-full ${hideBullets ? 'no-bullets' : ''}`, classname)}
		></trix-toolbar>
	{/await}
{/if}
```

# editor.svelte
Depends on TextArea from shadcn. 
My textArea is modified a bit so there is likely some code here that will break, so just remove it. I'll clean it up to behave like vanilla shadcn stuff later

```
<script lang="ts">
	import { browser } from '$app/environment';
	import TrixStarter from '@ernestasthedev/trix';
	import { cn } from '$lib/utils.js';
	import { Textarea } from '$lib/components/ui/textarea/index.js';

	let {
		id,
		input,
		value = $bindable(),
		placeholder,
		toolbar,
		rows = 1,
		class: classname,
		previewDisabled = false
	}: {
		id: string;
		input: string;
		value: string;
		toolbar: string;
		rows?: number;
		placeholder?: string;
		class?: string;
		previewDisabled?: boolean;
	} = $props();

	let loaded = $state(false);
	let trixEditorElement: HTMLElement | null = $state(null);
	let textAreaElement: HTMLTextAreaElement | null = $state(null);

	let cursorPosition: { start: number; end: number } = $state({ start: 0, end: 0 });

	function swapCursor() {
		const textareaElement = document.getElementById(input) as HTMLTextAreaElement;
		loaded = true;
		if (textareaElement && trixEditorElement && document.activeElement === textareaElement) {
			trixEditorElement.focus();
			(trixEditorElement as any).editor.setSelectedRange([
				cursorPosition.start,
				cursorPosition.end
			]);
		}
	}

	$effect.pre(() => {
		document.addEventListener('trix-before-initialize', (e) => {
			if (e.target instanceof HTMLElement && e.target.id === id) {
				textAreaElement = document.getElementById(input) as HTMLTextAreaElement;
				cursorPosition = {
					start: textAreaElement.selectionStart,
					end: textAreaElement.selectionEnd
				};
			}
		});
		document.addEventListener('trix-initialize', (e) => {
			if (e.target instanceof HTMLElement && e.target.id === id) {
				swapCursor();
			}
		});
	});
</script>

<Textarea
	{placeholder}
	class={cn(`${loaded ? 'hidden' : ''} pl-1 pr-0 w-full resize-none`, classname)}
	id={input}
	blended
	expand
	{rows}
	disabled={previewDisabled}
	bind:value
/>
{#if browser}
	{#await (async () => {
		await TrixStarter.init();
	})() then}
		<trix-editor
			{id}
			{input}
			{placeholder}
			{toolbar}
			class={cn(
				'border-x-0 border-t-0 border-b-2 p-2 pl-1 pr-0 break-all border-input placeholder:text-muted-foreground focus-visible:ring-ring bg-transparent  focus-visible:outline-none focus-visible:ring-1 disabled:cursor-not-allowed disabled:opacity-50',
				classname
			)}
			ontrix-change={(e: any) => {
				value = e.target.value;
			}}
			bind:this={trixEditorElement}
		></trix-editor>
	{/await}
{/if}
```

# Usage
If we assume this is part of a form using superForms:
```
<EditorToolbar id="foo"></EditorToolbar>
<Editor toolbar="foo" id="bar" input={attrs.id} bind:value={$formData.editor}></Editor>
```
Then to validate input, in your script add this:
```
	$effect.pre(() => {
		document.addEventListener('trix-change', (element: any) => {
			form.validate(element?.target?.id);
		});
	});
```
