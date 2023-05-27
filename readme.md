# webext-storage-cache [![](https://img.shields.io/npm/v/webext-storage-cache.svg)](https://www.npmjs.com/package/webext-storage-cache)

> Map-like promised cache storage with expiration. WebExtensions module for Chrome, Firefox, Safari

This module works on content scripts, background pages and option pages.

## Install

You can download the [standalone bundle](https://bundle.fregante.com/?pkg=webext-storage-cache&global=window) and include it in your `manifest.json`.

Or use `npm`:

```sh
npm install webext-storage-cache
```

## Usage

This module requires the `storage` permission and it’s suggested to also use `alarms` to safely schedule cache purging:

```json5
/* manifest.json */
{
	"permissions": [
		"storage",
		"alarms"
	],
	"background": {
		"scripts": [
			/* Remember to include/import it in the background to enable expired cache purging */
			"webext-storage-cache.js"
		]
	}
}
```

```js
import {CacheItem} from 'webext-storage-cache';

const item = new CacheItem('unique', {
	maxAge: {
		days: 3,
	},
});

(async () => {
	if (!(await item.isCached())) {
		const cachableItem = await someFunction();
		await item.set(cachableItem);
	}

	console.log(await item.get());
})();
```

The same code could also be written more effectively with `UpdatableCacheItem`:

```js
import {UpdatableCacheItem} from 'webext-storage-cache';

const item = new CacheItem('unique', {
	updater: someFunction,
	maxAge: {
		days: 3,
	},
});

(async () => {
	console.log(await cachedFunction());
})();
```

## API

- [CacheItem](./source/cache-item.md) - A simple API getter/setter
- [UpdatableCacheItem](./source/updatable-cache-item.md) - A memoize-like API to cache your function calls without manually calling `isCached`/`get`/`set`
- `globalCache` - Global helpers, documented below

### globalCache.clear()

Clears the cache. This is a special method that acts on the entire cache of the extension.

```js
import {globalCache} from 'webext-storage-cache';

document.querySelector('.options .clear-cache').addEventListener('click', async () => {
	await globalCache.clear()
})
```

## Related

- [webext-detect-page](https://github.com/fregante/webext-detect-page) - Detects where the current browser extension code is being run.
- [webext-options-sync](https://github.com/fregante/webext-options-sync) - Helps you manage and autosave your extension's options.
- [webext-base-css](https://github.com/fregante/webext-base-css) - Extremely minimal stylesheet/setup for Web Extensions’ options pages (also dark mode)
- [webext-dynamic-content-scripts](https://github.com/fregante/webext-dynamic-content-scripts) - Automatically registers your content_scripts on domains added via permission.request
- [More…](https://github.com/fregante/webext-fun)

## License

MIT © [Federico Brigante](https://fregante.com)
