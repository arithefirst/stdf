<script lang="ts">
	import { slide } from 'svelte/transition';
	import ModeSwitch from '../modeSwitch/ModeSwitch.svelte';
	import ThemeSwitch from '../themeSwitch/ThemeSwitch.svelte';
	import { isShowNavStore, isShowFundStore, showThemeSwitchStore } from '../../store';
	import { page } from '$app/stores';

	let { showLeftNav = false, showBottonLine = false, onclickCmdK } = $props();

	let currentRoute = $state('/guide');
	$effect(() => {
		currentRoute = $page.url.pathname;
	});
	let showNav = $state(false); //是否显示导航
	const toggleNavFun = () => {
		isShowNavStore.set(!$isShowNavStore);
	};
	//判断是 Mac 系统
	const isMac = navigator.userAgent.toUpperCase().indexOf('MAC') >= 0;
	const cmdkey = isMac ? '⌘' : 'Ctrl';
	const isZh = localStorage.getItem('lang') === 'zh_CN';

	// const dispatch = createEventDispatcher(); //创建事件派发器
	const clickCmdKFun = () => {
		onclickCmdK?.();
	};
	// 切换语言
	const toggleLangFunc = () => {
		// 获取当前页面地址
		const currentUrl = window.location.href;
		// url 是否包含参数 ?
		const hasQuery = currentUrl.includes('?');
		// 刷新 url，如果当前语言为中文，则跳转至英文，反之亦然，拼接 currentUrl + ?lang=${isZh?'en_US':'zh_CN'}
		window.location.href = `${currentUrl}${hasQuery ? '&' : '?'}lang=${isZh ? 'en_US' : 'zh_CN'}`;
		setTimeout(() => {
			window.location.reload();
		}, 300);
	};
	// 切换支持
	const toggleFundFunc = () => {
		isShowFundStore.set(true);
	};
	// 显示主题切换
	const showThemeFunc = () => {
		showThemeSwitchStore.set(!$showThemeSwitchStore);
	};
	let ThemeSwitchDom: HTMLButtonElement | null = $state(null);
	// 监听 window 点击事件，如果点击的不是 ThemeSwitchDom，则隐藏主题切换
	window.addEventListener('click', (e: MouseEvent) => {
		if (ThemeSwitchDom && !ThemeSwitchDom.contains(e.target as Node)) {
			showThemeSwitchStore.set(false);
		}
	});

	let showVersion = $state(false);
</script>

<div class="sticky top-0 z-[100] flex h-14 items-center justify-between border-b border-black/5 backdrop-blur-sm dark:border-white/10">
	{#if showLeftNav}
		<button class="cursor-pointer p-4 md:hidden" onclick={toggleNavFun}>
			{#if !$isShowNavStore}
				<svg width="24" height="24" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
					<path d="M21.0001 4.50004L7.50007 4.5L7.50006 6L21.0001 6.00004V4.50004Z" fill="currentColor" />
					<path d="M5.25007 4.5L3.00098 4.5L3.00098 6L5.25007 6V4.5Z" fill="currentColor" />
					<path d="M7.50007 11.2501L21.0001 11.2501V12.7501L7.50006 12.7501L7.50007 11.2501Z" fill="currentColor" />
					<path d="M3.00098 11.2501H5.25007V12.7501H3.00098L3.00098 11.2501Z" fill="currentColor" />
					<path d="M7.50007 18L21.0001 18V19.5L7.50006 19.5L7.50007 18Z" fill="currentColor" />
					<path d="M3.00098 18H5.25007V19.5H3.00098L3.00098 18Z" fill="currentColor" />
				</svg>
			{:else}
				<svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none">
					<path
						fill="currentColor"
						d="M10.4143 4.58594L10.4142 11.0003L16.0003 11.0004L16.0003 13.0004L10.4142 13.0003L10.4141 19.4144L3 12.0002L10.4143 4.58594ZM18.0002 19.0002V5.00018H20.0002V19.0002H18.0002Z"
					/>
				</svg>
			{/if}
		</button>
	{/if}

	<div class="flex items-end">
		<a href="/" class="flex items-center justify-between py-2 pl-6" aria-label={isZh ? '首页' : 'Home'}>
			<div class="fill-primary flex h-8 w-16 flex-col items-center justify-center" title={isZh ? '首页' : 'Home'}>
				<svg viewBox="0 0 90 80">
					<path
						class="fill-primary dark:fill-dark"
						d="M0 0H20H40H50C64.8056 0 77.7325 8.04398 84.6487 20H50H40V22.6757V30H50C55.5229 30 60 34.4771 60 40C60 45.5229 55.5229 50 50 50H40V57.3243V78.7398V80H20V66.4583V20H15.3513H0V0ZM50 80C72.0914 80 90 62.0914 90 40C90 36.547 89.5625 33.1962 88.7398 30H67.3244C69.0261 32.9417 70 36.3571 70 40C70 51.0457 61.0457 60 50 60V80Z"
					/>
					<path class="fill-dark dark:fill-primary" d="M20 30V0L0 50H20V80L40 30H20Z" />
				</svg>
			</div>
		</a>
		<!-- 下拉选项，选择版本 -->
		{#if $page.url.pathname === '' || $page.url.pathname === '/'}
			<div class="relative bottom-1">
				<button class="flex rounded bg-black/5 py-0.5 pl-2 pr-4 text-sm dark:bg-white/20" onclick={() => (showVersion = !showVersion)}>
					v1
					<span class="absolute bottom-0.5 left-5 w-4 text-gray-500">
						<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor">
							<path d="M12 15.0006L7.75732 10.758L9.17154 9.34375L12 12.1722L14.8284 9.34375L16.2426 10.758L12 15.0006Z"></path>
						</svg>
					</span>
				</button>
				{#if showVersion}
					<div class="absolute left-0 top-8 w-12 rounded-md bg-white p-1 text-xs shadow-md dark:bg-black dark:shadow-white/10">
						<a href="/" class="relative flex rounded py-0.5 pl-2 hover:bg-black/5 dark:hover:bg-white/20"
							>v1
							<span class="absolute bottom-0.5 left-4 ml-2 w-3">
								<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor">
									<path d="M9.9997 15.1709L19.1921 5.97852L20.6063 7.39273L9.9997 17.9993L3.63574 11.6354L5.04996 10.2212L9.9997 15.1709Z"
									></path>
								</svg>
							</span>
						</a>
						<a href="https://0.stdf.design" target="_blank" class="mt-2 block rounded px-2 py-0.5 hover:bg-black/5 dark:hover:bg-white/20">
							v0
						</a>
					</div>
				{/if}
			</div>
		{/if}
	</div>
	<div>
		<div class="cursor-pointer p-4 md:hidden">
			<button onclick={() => (showNav = !showNav)}>
				{#if !showNav}
					<svg width="24" height="24" fill="none" aria-hidden="true" xmlns="http://www.w3.org/2000/svg">
						<path
							d="M12 6v.01M12 12v.01M12 18v.01M12 7a1 1 0 1 1 0-2 1 1 0 0 1 0 2Zm0 6a1 1 0 1 1 0-2 1 1 0 0 1 0 2Zm0 6a1 1 0 1 1 0-2 1 1 0 0 1 0 2Z"
							stroke="currentColor"
							stroke-width="1.5"
							stroke-linecap="round"
							stroke-linejoin="round"
						/>
					</svg>
				{:else}
					<svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none">
						<path
							fill="currentColor"
							d="M12 22C6.47715 22 2 17.5228 2 12C2 6.47715 6.47715 2 12 2C17.5228 2 22 6.47715 22 12C22 17.5228 17.5228 22 12 22ZM12 20C16.4183 20 20 16.4183 20 12C20 7.58172 16.4183 4 12 4C7.58172 4 4 7.58172 4 12C4 16.4183 7.58172 20 12 20ZM12 10.5858L14.8284 7.75736L16.2426 9.17157L13.4142 12L16.2426 14.8284L14.8284 16.2426L12 13.4142L9.17157 16.2426L7.75736 14.8284L10.5858 12L7.75736 9.17157L9.17157 7.75736L12 10.5858Z"
						/>
					</svg>
				{/if}
			</button>
			{#if showNav}
				<!-- 移动端 -->
				<div
					class="absolute right-2 top-16 flex flex-col space-y-4 rounded-sm border border-black/10 bg-white p-1 pt-4 text-center font-bold shadow-lg backdrop-blur-sm dark:border-white/20 dark:bg-gray-950 dark:shadow-white/20"
				>
					<a
						href="/guide"
						class="rounded-sm px-4 py-1 hover:bg-gray-100 dark:hover:bg-gray-700"
						class:bg-gray-100={currentRoute === '/guide'}
						class:dark:bg-gray-500={currentRoute === '/guide'}>{isZh ? '指南' : 'Guide'}</a
					>
					<a
						href="/components?nav=button&tab=0"
						class="rounded-sm px-4 py-1 hover:bg-gray-100 dark:hover:bg-gray-700"
						class:bg-gray-100={currentRoute === '/components'}
						class:dark:bg-gray-500={currentRoute === '/components'}>{isZh ? '组件' : 'Components'}</a
					>
					<button
						class="relative rounded-sm px-4 py-1 hover:bg-gray-100 dark:hover:bg-gray-700"
						onclick={showThemeFunc}
						bind:this={ThemeSwitchDom}
					>
						{isZh ? '主题' : 'Theme'}
						{#if $showThemeSwitchStore}
							<div
								transition:slide={{ duration: 300, axis: 'y' }}
								class="absolute right-4 top-0 rounded-lg bg-white p-4 shadow-lg dark:bg-black/95 dark:shadow-white/10"
							>
								<ModeSwitch useViewTransition={false} />
								<ThemeSwitch vertical />
							</div>
						{/if}
					</button>
					<button class="rounded-sm px-4 py-1 text-center hover:bg-gray-100 dark:hover:bg-gray-700" onclick={toggleFundFunc}>
						{isZh ? '支持' : 'Support'}
					</button>

					<!-- 语言切换 -->
					<button
						aria-label={isZh ? '切换语言' : 'Switch Language'}
						class="rounded-sm px-4 py-1 text-center hover:bg-gray-100 dark:hover:bg-gray-700"
						onclick={toggleLangFunc}
					>
						<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="20" height="20" class="inline-block">
							<path fill="none" d="M0 0h24v24H0z" />
							<path
								fill="currentColor"
								d="M5 15v2a2 2 0 0 0 1.85 1.995L7 19h3v2H7a4 4 0 0 1-4-4v-2h2zm13-5l4.4 11h-2.155l-1.201-3h-4.09l-1.199 3h-2.154L16 10h2zm-1 2.885L15.753 16h2.492L17 12.885zM8 2v2h4v7H8v3H6v-3H2V4h4V2h2zm9 1a4 4 0 0 1 4 4v2h-2V7a2 2 0 0 0-2-2h-3V3h3zM6 6H4v3h2V6zm4 0H8v3h2V6z"
							/>
						</svg>
					</button>
					<!-- GitHub -->
					<a
						href="https://github.com/any-tdf/stdf"
						class="rounded-sm px-4 py-1 text-center hover:bg-gray-100 dark:hover:bg-gray-700"
						target="_blank"
						aria-label={isZh ? '跳转至 GitHub' : 'Jump to GitHub'}
					>
						<svg width="20" height="20" viewBox="0 0 24 24" class="inline-block" xmlns="http://www.w3.org/2000/svg">
							<path
								fill-rule="evenodd"
								clip-rule="evenodd"
								d="M12 2.23999C6.475 2.23999 2 6.71499 2 12.24C2 16.665 4.8625 20.4025 8.8375 21.7275C9.3375 21.815 9.525 21.515 9.525 21.2525C9.525 21.015 9.5125 20.2275 9.5125 19.39C7 19.8525 6.35 18.7775 6.15 18.215C6.0375 17.9275 5.55 17.04 5.125 16.8025C4.775 16.615 4.275 16.1525 5.1125 16.14C5.9 16.1275 6.4625 16.865 6.65 17.165C7.55 18.6775 8.9875 18.2525 9.5625 17.99C9.65 17.34 9.9125 16.9025 10.2 16.6525C7.975 16.4025 5.65 15.54 5.65 11.715C5.65 10.6275 6.0375 9.72749 6.675 9.02749C6.575 8.77749 6.225 7.75249 6.775 6.37749C6.775 6.37749 7.6125 6.11499 9.525 7.40249C10.325 7.17749 11.175 7.06499 12.025 7.06499C12.875 7.06499 13.725 7.17749 14.525 7.40249C16.4375 6.10249 17.275 6.37749 17.275 6.37749C17.825 7.75249 17.475 8.77749 17.375 9.02749C18.0125 9.72749 18.4 10.615 18.4 11.715C18.4 15.5525 16.0625 16.4025 13.8375 16.6525C14.2 16.965 14.5125 17.565 14.5125 18.5025C14.5125 19.84 14.5 20.915 14.5 21.2525C14.5 21.515 14.6875 21.8275 15.1875 21.7275C17.1727 21.0573 18.8977 19.7815 20.1198 18.0795C21.3419 16.3776 21.9995 14.3352 22 12.24C22 6.71499 17.525 2.23999 12 2.23999Z"
								fill="currentColor"
							/>
						</svg>
					</a>
				</div>
			{/if}
		</div>
		<!--PC 端-->
		<div class="hidden items-center space-x-2 px-6 font-bold md:flex">
			<button
				class="flex cursor-pointer rounded-sm border border-black/10 px-2 text-sm font-normal text-black/40 transition-all hover:border-black/20 dark:border-white/10 dark:text-white/40 dark:hover:border-white/20"
				style="padding-top: 0.3125rem;padding-bottom: 0.3125rem;"
				onclick={clickCmdKFun}
			>
				<div class="mr-1.5 flex flex-col justify-center">
					<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="16" height="16" style="fill: currentColor;">
						<path fill="none" d="M0 0h24v24H0z" />
						<path
							d="M11 2c4.968 0 9 4.032 9 9s-4.032 9-9 9-9-4.032-9-9 4.032-9 9-9zm0 16c3.867 0 7-3.133 7-7 0-3.868-3.133-7-7-7-3.868 0-7 3.132-7 7 0 3.867 3.132 7 7 7zm8.485.071l2.829 2.828-1.415 1.415-2.828-2.829 1.414-1.414z"
						/>
					</svg>
				</div>
				<div class="mr-4 flex flex-col justify-center text-xs">
					{isZh ? '快速搜索...' : 'Quick search...'}
				</div>
				<div class="mr-1 rounded-sm bg-black/5 px-1 font-bold dark:bg-white/5">{cmdkey}</div>
				<div class="rounded-sm bg-black/5 px-1.5 font-bold dark:bg-white/5">K</div>
			</button>
			<a
				href="/guide"
				class="rounded-sm px-4 py-1 transition-all {currentRoute === '/guide'
					? 'bg-primary dark:bg-dark text-white dark:text-black'
					: 'hover:bg-black/5 dark:hover:bg-white/5'}"
			>
				{isZh ? '指南' : 'Guide'}
			</a>
			<a
				href="/components?nav=button&tab=0"
				class="inline-block rounded-sm px-4 py-1 text-center transition-all {currentRoute === '/components'
					? 'bg-primary dark:bg-dark text-white dark:text-black'
					: 'hover:bg-black/5 dark:hover:bg-white/5'}"
			>
				{isZh ? '组件' : 'Components'}
			</a>
			<button
				class="relative rounded-sm px-4 py-1 text-center transition-all hover:bg-black/5 dark:hover:bg-white/5"
				onmouseover={() => showThemeSwitchStore.set(true)}
				onfocus={() => showThemeSwitchStore.set(true)}
				onblur={() => showThemeSwitchStore.set(false)}
				onmouseout={() => showThemeSwitchStore.set(false)}
				bind:this={ThemeSwitchDom}
			>
				{isZh ? '主题' : 'Theme'}
				{#if $showThemeSwitchStore}
					<div transition:slide={{ duration: 300, axis: 'y' }} class="absolute left-1/2 top-8 -translate-x-1/2 pt-2">
						<div class="rounded-lg bg-white p-4 shadow-lg dark:bg-black/95 dark:shadow-white/10">
							<ModeSwitch useViewTransition={false} />
							<ThemeSwitch vertical />
						</div>
					</div>
				{/if}
			</button>
			<button
				class="cursor-pointer rounded-sm px-4 py-1 text-center transition-all hover:bg-black/5 dark:hover:bg-white/5"
				title={isZh ? '支持' : 'Support'}
				onclick={toggleFundFunc}
			>
				{isZh ? '支持' : 'Support'}
			</button>
			<!-- 语言切换 -->
			<button
				onclick={toggleLangFunc}
				title={isZh ? '切换语言' : 'Switch languages'}
				aria-label={isZh ? '切换语言' : 'Switch languages'}
				class="cursor-pointer rounded-sm px-4 py-1 text-center transition-all hover:bg-black/5 dark:hover:bg-white/5"
			>
				<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="20" height="20" class="inline-block">
					<path fill="none" d="M0 0h24v24H0z" />
					<path
						fill="currentColor"
						d="M5 15v2a2 2 0 0 0 1.85 1.995L7 19h3v2H7a4 4 0 0 1-4-4v-2h2zm13-5l4.4 11h-2.155l-1.201-3h-4.09l-1.199 3h-2.154L16 10h2zm-1 2.885L15.753 16h2.492L17 12.885zM8 2v2h4v7H8v3H6v-3H2V4h4V2h2zm9 1a4 4 0 0 1 4 4v2h-2V7a2 2 0 0 0-2-2h-3V3h3zM6 6H4v3h2V6zm4 0H8v3h2V6z"
					/>
				</svg>
			</button>
			<!-- GitHub -->
			<a
				href="https://github.com/any-tdf/stdf"
				class="inline-block rounded-sm px-4 py-1 transition-all hover:bg-black/5 dark:hover:bg-white/5"
				target="_blank"
				aria-label={isZh ? '跳转至 GitHub' : 'Jump to GitHub'}
				title={isZh ? '跳转至 GitHub' : 'Jump to GitHub'}
			>
				<svg width="20" height="20" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg" class="inline-block">
					<path
						fill-rule="evenodd"
						clip-rule="evenodd"
						d="M12 2.23999C6.475 2.23999 2 6.71499 2 12.24C2 16.665 4.8625 20.4025 8.8375 21.7275C9.3375 21.815 9.525 21.515 9.525 21.2525C9.525 21.015 9.5125 20.2275 9.5125 19.39C7 19.8525 6.35 18.7775 6.15 18.215C6.0375 17.9275 5.55 17.04 5.125 16.8025C4.775 16.615 4.275 16.1525 5.1125 16.14C5.9 16.1275 6.4625 16.865 6.65 17.165C7.55 18.6775 8.9875 18.2525 9.5625 17.99C9.65 17.34 9.9125 16.9025 10.2 16.6525C7.975 16.4025 5.65 15.54 5.65 11.715C5.65 10.6275 6.0375 9.72749 6.675 9.02749C6.575 8.77749 6.225 7.75249 6.775 6.37749C6.775 6.37749 7.6125 6.11499 9.525 7.40249C10.325 7.17749 11.175 7.06499 12.025 7.06499C12.875 7.06499 13.725 7.17749 14.525 7.40249C16.4375 6.10249 17.275 6.37749 17.275 6.37749C17.825 7.75249 17.475 8.77749 17.375 9.02749C18.0125 9.72749 18.4 10.615 18.4 11.715C18.4 15.5525 16.0625 16.4025 13.8375 16.6525C14.2 16.965 14.5125 17.565 14.5125 18.5025C14.5125 19.84 14.5 20.915 14.5 21.2525C14.5 21.515 14.6875 21.8275 15.1875 21.7275C17.1727 21.0573 18.8977 19.7815 20.1198 18.0795C21.3419 16.3776 21.9995 14.3352 22 12.24C22 6.71499 17.525 2.23999 12 2.23999Z"
						fill="currentColor"
					/>
				</svg>
			</a>
		</div>
	</div>
</div>
