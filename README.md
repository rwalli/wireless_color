# wireless_color

Colorized LuCI **Wireless** page: each radio gets its own hue and its SSIDs get
neighbouring hues, so you can see at a glance which radio and which SSID
anything belongs to. In *Wireless Overview* the badge is tinted as a whole; in
*Associated Stations* the color runs as a stripe along the bottom of each row's
badge, in the color of the network the client is on.

It is upstream [`wireless.js` from luci `master`][upstream] with a purely
additive 40-line patch — nothing is removed. A fork that replays the patch onto
upstream every night lives at [rwalli/luci][fork], branch `wireless-color`.

`src/wireless.js` is the finished file, ready to copy onto a router.
[`wireless-color.patch`](wireless-color.patch) is the change on its own — GitHub
cannot render a diff for the 110 KB finished file, so read the patch instead, or
the [compare view][compare] against upstream.

[upstream]: https://github.com/openwrt/luci/blob/master/modules/luci-mod-network/htdocs/luci-static/resources/view/network/wireless.js
[fork]: https://github.com/rwalli/luci/tree/wireless-color
[compare]: https://github.com/openwrt/luci/compare/master...rwalli:luci:wireless-color

## Updating

The [fork][fork] replays the patch onto upstream `master` every night, so its
branch is always current; it opens an issue if the patch stops applying. To
refresh this copy by hand, fetch the current upstream file and replay the patch
onto it:

```sh
P=modules/luci-mod-network/htdocs/luci-static/resources/view/network/wireless.js
curl -sSLO https://raw.githubusercontent.com/openwrt/luci/master/$P
patch -F0 wireless.js < wireless-color.patch
```

## Install

On the router:

```sh
cp -a /www/luci-static/resources/view/network/wireless.js /www/luci-static/resources/view/network/wireless.js.orig
wget -O /www/luci-static/resources/view/network/wireless.js https://raw.githubusercontent.com/rwalli/wireless_color/refs/heads/main/src/wireless.js
```

Then reload the Wireless page with **Ctrl+Shift+R** to get past the browser cache.

## Uninstall

```sh
mv /www/luci-static/resources/view/network/wireless.js.orig /www/luci-static/resources/view/network/wireless.js
```

## Tuning

All knobs live in one constant near the top of `src/wireless.js`:

```js
const wifiColors = { radioSat: 75, radioLight: 80, netSat: 75, netLight: 80, netSpread: 40, lightModeBoost: 25, stripe: 17 };
```

`*Sat` / `*Light` are HSL saturation and lightness in percent — lower the
lightness for stronger colors. `netSpread` is how many degrees apart the SSIDs
of one radio are placed. `lightModeBoost` is extra saturation added on the light
theme, where the tints otherwise wash out; the dark theme uses `*Sat` unchanged.
`stripe` is the height in pixels of the bottom stripe in *Associated Stations*,
which is painted with the same tint as the overview badges.
