<script lang="ts">
	import { t } from '$lib/convert';

	type Dictionary = {
		url: string;
		name: string;
		dialect: string;
		languages: {
			language: string;
			label: string;
		}[];
		count: number;
	};

	const dictionaries: Dictionary[] = [
		{
			url: 'https://ainugo.nam.go.jp/dic',
			name: '国立アイヌ民族博物館アイヌ語アーカイブ',
			dialect: 'Sar',
			languages: [
				{ language: 'ain', label: 'Aynuitak-Sisam’itak' },
				{ language: 'ja', label: 'アイヌ語-日本語' }
			],
			count: 27000
		},
		{
			url: 'http://hdl.handle.net/2115/87707',
			name: '和愛辞典 : 草稿版 （太田満 編）',
			dialect: 'Iskar',
			languages: [
				{ language: 'ain', label: 'Sisam’itak-Aynuitak' },
				{ language: 'ja', label: '日本語-アイヌ語' }
			],
			count: 12500
		},
		{
			url: 'http://itelmen.placo.net/Ainu-archives/index.html',
			name: 'アイヌ語鵡川方言　日本語―アイヌ語辞典',
			dialect: 'Muka',
			languages: [
				{ language: 'ain', label: 'Sisam’itak-Aynuitak' },
				{ language: 'ja', label: '日本語-アイヌ語' }
			],
			count: 6300
		},
		{
			url: 'https://ainu.ninjal.ac.jp/topic/',
			name: 'トピック別 アイヌ語会話辞典',
			dialect: 'Sar',
			languages: [
				{ language: 'ain', label: 'Sisam’itak-Aynuitak' },
				{ language: 'ja', label: '日本語-アイヌ語' }
			],
			count: 3500
		},
		{
			url: 'https://ainu.ninjal.ac.jp/topic/en/',
			name: 'Topical Dictionary of Conversational Ainu',
			dialect: 'Sar',
			languages: [
				{ language: 'ain', label: 'Inkiriskur’itak-Sisam’itak' },
				{ language: 'en', label: 'English-Ainu' }
			],
			count: 3500
		},
		{
			url: 'https://ja.wiktionary.org/wiki/%E3%82%AB%E3%83%86%E3%82%B4%E3%83%AA:%E3%82%A2%E3%82%A4%E3%83%8C%E8%AA%9E',
			name: '日本語版Wiktionary',
			dialect: '-',
			languages: [
				{ language: 'ain', label: 'Aynuitak-Sisam’itak' },
				{ language: 'ja', label: 'アイヌ語-日本語' }
			],
			count: 2000
		},
		{
			url: 'https://itak.aynu.org/',
			name: 'Tane an Aynuitak-kotupte Itak-uwoeroskip\n現代アイヌ語翻訳用語集\nModern Ainu Translation Glossary',
			dialect: '-',
			languages: [
				{ language: 'ain', label: 'Aynuitak-Sisam’itak-Inkiriskur’itak' },
				{ language: 'ja', label: 'アイヌ語-日本語' },
				{ language: 'en', label: 'Ainu-Japanese-English' }
			],
			count: 1000
		},
		{
			url: 'https://en.wiktionary.org/wiki/Category:Ainu_language',
			name: 'English Wiktionary',
			dialect: '-',
			languages: [
				{ language: 'ain', label: 'Aynuitak-Inkiriskur’itak' },
				{ language: 'en', label: 'Ainu-English' }
			],
			count: 600
		}
	];
</script>

<ul class="flex flex-col gap-4 md:hidden">
	{#each dictionaries as { url, name, languages, dialect, count }}
		<li class="hover:bg-gray-100/50 flex flex-col px-4 py-2">
			<a href={url} lang="ja" target="_blank" class="text-lg font-bold"
				>{@html name.replaceAll(/\n/g, '<br />')}</a
			>
			<span class="flex flex-row gap-x-4 flex-wrap">
				{#each languages as { language, label }, i}
					<span lang={language}>{i === 0 ? label.split('-').map($t).join('-') : label}</span>
				{/each}
			</span>
			<span>
				<span class="font-bold">{$t('Iposse')}</span>
				{dialect === '-' ? '-' : $t(dialect)}
			</span>
			<span>
				<span class="font-bold">{$t('Cipiskip')}</span>: {count}
			</span>
		</li>
	{/each}
</ul>

<table class="hidden md:block">
	<thead>
		<tr>
			<th>{$t('Ieonnekunnep')}</th>
			<th>{$t('Itak')}</th>
			<th>{$t('Iposse')}</th>
			<th>{$t('Cipiskip')}</th>
		</tr>
	</thead>
	<tbody>
		{#each dictionaries as { url, name, languages, dialect, count }}
			<tr class="hover:bg-gray-100/50">
				<td>
					<a href={url} lang="ja" target="_blank">{@html name.replaceAll(/\n/g, '<br />')}</a>
				</td>
				<td>
					{#each languages as { language, label }, i}
						<span lang={language}>{i === 0 ? label.split('-').map($t).join('-') : label}</span><br
						/>
					{/each}
				</td>
				<td> {dialect === '-' ? '-' : $t(dialect)} </td>
				<td> {count} </td>
			</tr>
		{/each}
	</tbody>
</table>

<style>
	table {
		border-collapse: collapse;
	}

	th,
	td {
		border-top: 1px solid #ccc;
		border-bottom: 1px solid #ccc;
		padding: 0.25em 0.5em;
	}

	th:first-child {
		border-left: 0;
	}

	td:first-child {
		border-left: 0;
	}

	th:last-child {
		border-right: 0;
	}

	td:last-child {
		text-align: right;
		border-right: 0;
	}
</style>
