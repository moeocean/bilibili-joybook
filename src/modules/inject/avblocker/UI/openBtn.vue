<template>
<button @click.prevent="openSetting" class="joybook-filter-setting">
	过滤
	<img :src="`chrome-extension://${extensionId}${settingsIcon}`">
</button>
</template>

<script lang='ts'>
import { Component, Prop, Vue } from 'vue-property-decorator';
import { waitUntilDomLoaded } from '../../../../utils/helper';
@Component
export default class OpenSettingBtn extends Vue {
	public name: string = 'OpenSettingBtn';
	public extensionId: string = window.joybook.id;
	public settingsIcon: string = require('@/assets/twotone-settings-24px.svg');

	@Prop({ type: String, default: '' }) public section!: string;

	public openSetting() {
		chrome.runtime.sendMessage(
			window.joybook.id,
			{
				name: 'serve',
				cmd: 'create:tabs',
				payload: [{
					url: `chrome-extension://${this.extensionId}/options.html#/redirect?to=avblocker?section=${this.section}`,
				}],
			},
			() => {},
		);
	}

	public mounted() {
		if (!document.querySelector('#avblockerStyle')) {
			const style = document.createElement('style');
			style.id = 'avblockerStyle';
			style.innerText = `.joybook-filter-setting{float:right;border:1px solid #ccd0d7;border-radius:4px;cursor:pointer;background-color:#fff;height:24px;line-height:22px;padding:0 10px;-webkit-transition:all .2s;transition:all .2s}.joybook-filter-setting:hover{background-color:#ccd0d7}.joybook-filter-setting:hover img{-webkit-transform:rotate(1turn);transform:rotate(1turn)}.joybook-filter-setting img{width:16px;vertical-align:sub;-webkit-transition:all .5s;transition:all .5s}.read-push{margin:0 0 0 10px}`;
			document.head.appendChild(style);
		}

		waitUntilDomLoaded<HTMLElement>(`#bili_${this.section} .zone-title > div`, false)
			.then(dom => {
				dom.appendChild(this.$el);
			});
	}
}
</script>
