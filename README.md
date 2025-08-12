# Description
This Tampermonkey script replaces the header on [lostcity.rs](https://2004.lostcity.rs/) with classic RuneScape-style banners. It lets you enjoy an authentic RuneScape look and feel while ensuring Lost City avoids any legal issues by using their original assets.
![chrome_PxxffK2kib](https://github.com/user-attachments/assets/fa0033ec-6d3d-4355-9be0-93a49bbcd75d)

## Code
```
// ==UserScript==
// @name         LostCity.RS Header Replacer
// @namespace    http://tampermonkey.net/
// @version      1.0
// @description  Replace LostCity header with classic RS ones
// @match        *://2004.lostcity.rs/*
// @grant        GM_getResourceURL
// @resource     navbar          https://raw.githubusercontent.com/halogod35/lostcity-iframe/master/img/gamewin/navbar.gif
// @resource     navbar_jagex    https://raw.githubusercontent.com/halogod35/lostcity-iframe/master/img/gamewin/navbar_jagex.gif
// @resource     navbar_mainmenu https://raw.githubusercontent.com/halogod35/lostcity-iframe/master/img/gamewin/navbar_mainmenu.gif
// @resource     navbar_worldmap https://raw.githubusercontent.com/halogod35/lostcity-iframe/master/img/gamewin/navbar_worldmap.gif
// @resource     navbar_manual   https://raw.githubusercontent.com/halogod35/lostcity-iframe/master/img/gamewin/navbar_manual.gif
// @resource     navbar_rules    https://raw.githubusercontent.com/halogod35/lostcity-iframe/master/img/gamewin/navbar_rules.gif
// @resource     navbar_members  https://raw.githubusercontent.com/halogod35/lostcity-iframe/master/img/gamewin/navbar_members.gif
// @resource     banner1         https://raw.githubusercontent.com/halogod35/lostcity-iframe/master/img/banners/banner_rule1.gif
// @resource     banner2         https://raw.githubusercontent.com/halogod35/lostcity-iframe/master/img/banners/banner_rule2.gif
// @resource     banner3         https://raw.githubusercontent.com/halogod35/lostcity-iframe/master/img/banners/banner_rule3.gif
// ==/UserScript==

(function() {
    'use strict';

    const banners = [
        GM_getResourceURL("banner1"),
        GM_getResourceURL("banner2"),
        GM_getResourceURL("banner3"),
    ];

     function getRandomBanner() {
        return banners[Math.floor(Math.random() * banners.length)];
    }

    function getRandomBannerIndex(excludeIndex) {
        let index;
        do {
            index = Math.floor(Math.random() * banners.length);
        } while (index === excludeIndex);
        return index;
    }

    const newHTML = `
<center>
<table cellpadding=0 cellspacing=1>
    <tr>
        <td align=center>
            <img id="rotating-banner" src="${getRandomBanner()}"
                 width="373" height="63" style="border:0;">
        </td>
    </tr>
    <tr>
        <td align=center>
            <table border=0 background="${GM_getResourceURL("navbar")}"
                   cellspacing=0 cellpadding=0 width=765 height=25>
                <tr>
                    <td width=5></td>
                    <td><img border=0 src="${GM_getResourceURL("navbar_jagex")}" style="vertical-align: middle"></td>
                    <td valign=center><a href="title" id="menulink"><img border=0 src="${GM_getResourceURL("navbar_mainmenu")}" style="vertical-align: middle"><font color=white style="font-family:Arial,Helvetica,sans-serif;font-size:11px;">Main Menu</font></a></td>
                    <td valign=center><a href="serverlist?hires.x=111&hires.y=30&method=0" id="menulink2"><img border=0 src="${GM_getResourceURL("navbar_mainmenu")}" style="vertical-align: middle"><font color=white style="font-family:Arial,Helvetica,sans-serif;font-size:11px;">World Select</font></a></td>
                    <td valign=center><a onclick="window.open('worldmap','WorldMap','width=635,height=550');return false;" href="worldmap" target="WorldMap"><img border=0 src="${GM_getResourceURL("navbar_worldmap")}" style="vertical-align: middle"><font color=white style="font-family:Arial,Helvetica,sans-serif;font-size:11px;">World Map</font></a></td>
                    <td valign=center><a href="https://2004hq.com" target="Manual"><img border=0 src="${GM_getResourceURL("navbar_manual")}" style="vertical-align: middle"><font color=white style="font-family:Arial,Helvetica,sans-serif;font-size:11px;">Manual</font></a></td>
                    <td valign=center><a href="originalRules" target="RulesSecurity"><img border=0 src="${GM_getResourceURL("navbar_rules")}" style="vertical-align: middle"><font color=white style="font-family:Arial,Helvetica,sans-serif;font-size:11px;">Rules & Security</font></a></td>
                </tr>
            </table>
        </td>
    </tr>
</table>
</center>
`;

    function replaceHeader() {
        const target = document.getElementById('gameframe-top');
        if (target && target.outerHTML !== newHTML) {
            target.outerHTML = newHTML;
            console.log("Header replaced");

            const bannerImg = document.getElementById("rotating-banner");

            // Track current banner index
            let currentIndex = banners.indexOf(bannerImg.src);
            if (currentIndex === -1) currentIndex = 0; // fallback

            // Change banner every 10 minutes automatically
            setInterval(() => {
                currentIndex = getRandomBannerIndex(currentIndex);
                bannerImg.src = banners[currentIndex];
            }, 10 * 60 * 1000);

            // Add click event to cycle banner on click
            bannerImg.addEventListener("click", () => {
                currentIndex = getRandomBannerIndex(currentIndex);
                bannerImg.src = banners[currentIndex];
            });
        }
    }

    replaceHeader();

    const observer = new MutationObserver(() => replaceHeader());
    observer.observe(document.body, { childList: true, subtree: true });
})();
```
