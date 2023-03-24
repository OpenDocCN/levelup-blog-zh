# 用户在线足迹

> 原文：<https://levelup.gitconnected.com/ethical-hacking-part-17-user-online-footprint-42c0a75f66f2>

## 如何对用户的在线足迹进行调查

![](img/5e590dc014c42cf9ad7637cbcd764cdc.png)

如果你曾经被黑客攻击或调查黑客，并试图拼凑什么已经做了，所有你能找到的是黑客的用户名，你可能是幸运的。或者，您可能有兴趣查看自己的在线足迹，或者感兴趣的人的在线足迹。我想给你介绍一个叫做[夏洛克](https://sherlock-project.github.io/)的神奇工具。

夏洛克[仓库可以在这里找到](https://github.com/sherlock-project/sherlock)。夏洛克使用 Python 3 运行，这是跨平台的，因此任何可以运行 Python 3 的系统都可以运行夏洛克。

## 夏洛克的安装

为了方便起见，我将在我的 Kali linux 实例上安装夏洛克。第一步是确保“ **Git** ”、“ **Python 3** ”和“ **PIP** ”都已安装。

```
kali@kali:~$ **sudo apt-get install git python3 pip -y**
Reading package lists... Done
Building dependency tree       
Reading state information... Done
Note, selecting 'python3-pip' instead of 'pip'
git is already the newest version (1:2.28.0-1).
git set to manually installed.
python3 is already the newest version (3.8.2-3).
python3-pip is already the newest version (20.1.1-2).
0 upgraded, 0 newly installed, 0 to remove and 149 not upgraded.
```

安装说明可以在夏洛克 [README.md](https://github.com/sherlock-project/sherlock/blob/master/README.md) 中找到，但是我也将在这里讨论它们。

```
kali@kali:~$ **git clone** [**https://github.com/sherlock-project/sherlock.git**](https://github.com/sherlock-project/sherlock.git)
Cloning into 'sherlock'...
remote: Enumerating objects: 64, done.
remote: Counting objects: 100% (64/64), done.
remote: Compressing objects: 100% (32/32), done.
remote: Total 4926 (delta 33), reused 47 (delta 22), pack-reused 4862
Receiving objects: 100% (4926/4926), 15.46 MiB | 6.35 MiB/s, done.
Resolving deltas: 100% (3102/3102), done.kali@kali:~$ **cd sherlock**kali@kali:~/sherlock$ **python3 -m pip install -r requirements.txt**
Requirement already satisfied: beautifulsoup4>=4.8.0 in /usr/lib/python3/dist-packages (from -r requirements.txt (line 1)) (4.9.3)
Requirement already satisfied: bs4>=0.0.1 in /home/kali/.local/lib/python3.8/site-packages (from -r requirements.txt (line 2)) (0.0.1)
Requirement already satisfied: certifi>=2019.6.16 in /usr/lib/python3/dist-packages (from -r requirements.txt (line 3)) (2020.6.20)
Requirement already satisfied: colorama>=0.4.1 in /usr/lib/python3/dist-packages (from -r requirements.txt (line 4)) (0.4.3)
Requirement already satisfied: lxml>=4.4.0 in /usr/lib/python3/dist-packages (from -r requirements.txt (line 5)) (4.6.1)
Requirement already satisfied: PySocks>=1.7.0 in /usr/lib/python3/dist-packages (from -r requirements.txt (line 6)) (1.7.1)
Requirement already satisfied: requests>=2.22.0 in /usr/lib/python3/dist-packages (from -r requirements.txt (line 7)) (2.24.0)
Requirement already satisfied: requests-futures>=1.0.0 in /usr/lib/python3/dist-packages (from -r requirements.txt (line 8)) (1.0.0)
Requirement already satisfied: soupsieve>=1.9.2 in /usr/lib/python3/dist-packages (from -r requirements.txt (line 9)) (2.0.1)
Requirement already satisfied: stem>=1.8.0 in /usr/lib/python3/dist-packages (from -r requirements.txt (line 10)) (1.8.0)
Requirement already satisfied: torrequest>=0.1.0 in /home/kali/.local/lib/python3.8/site-packages (from -r requirements.txt (line 11)) (0.1.0)
```

## 夏洛克的用法

让我们快速浏览一下夏洛克帮助，看看提供了什么…

```
kali@kali:~/sherlock$ **python3 sherlock -h**
usage: sherlock [-h] [--version] [--verbose] [--folderoutput FOLDEROUTPUT] [--output OUTPUT] [--tor] [--unique-tor] [--csv] [--site SITE_NAME] [--proxy PROXY_URL] [--json JSON_FILE] [--timeout TIMEOUT]
                [--print-all] [--print-found] [--no-color] [--browse] [--local]
                USERNAMES [USERNAMES ...]Sherlock: Find Usernames Across Social Networks (Version 0.13.0)positional arguments:
  USERNAMES             One or more usernames to check with social networks.optional arguments:
  -h, --help            show this help message and exit
  --version             Display version information and dependencies.
  --verbose, -v, -d, --debug
                        Display extra debugging information and metrics.
  --folderoutput FOLDEROUTPUT, -fo FOLDEROUTPUT
                        If using multiple usernames, the output of the results will be saved to this folder.
  --output OUTPUT, -o OUTPUT
                        If using single username, the output of the result will be saved to this file.
  --tor, -t             Make requests over Tor; increases runtime; requires Tor to be installed and in system path.
  --unique-tor, -u      Make requests over Tor with new Tor circuit after each request; increases runtime; requires Tor to be installed and in system path.
  --csv                 Create Comma-Separated Values (CSV) File.
  --site SITE_NAME      Limit analysis to just the listed sites. Add multiple options to specify more than one site.
  --proxy PROXY_URL, -p PROXY_URL
                        Make requests over a proxy. e.g. socks5://127.0.0.1:1080
  --json JSON_FILE, -j JSON_FILE
                        Load data from a JSON file or an online, valid, JSON file.
  --timeout TIMEOUT     Time (in seconds) to wait for response to requests. Default timeout is infinity. A longer timeout will be more likely to get results from slow sites. On the other hand, this may cause
                        a long delay to gather all results.
  --print-all           Output sites where the username was not found.
  --print-found         Output sites where the username was found.
  --no-color            Don't color terminal output
  --browse, -b          Browse to all results on default browser.
  --local, -l           Force the use of the local data.json file.
```

那里有一些非常有趣的选择。我看到它支持我在另一篇文章中提到的 TOR，“道德黑客(第 6 部分):匿名化(T13)”。

```
--tor, -t             Make requests over Tor; increases runtime; requires Tor to be installed and in system path.
  --unique-tor, -u      Make requests over Tor with new Tor circuit after each request; increases runtime; requires Tor to be installed and in system path.
```

默认选项是“ **— print-found** ”，但您也可以“ **— print-all** ”，这将显示它正在检查的所有站点，以及是否找到了用户。

如果您在可以打开浏览器的 GUI 中运行夏洛克，这个选项看起来也很有趣。

```
--browse, -b          Browse to all results on default browser.
```

所以它的工作方式如下…

我打开 Instagram，只是在列表中寻找第一个公开的个人资料，它是“ **charlottedewittemusic** ”。我们知道 DJ 在 Instagram 上，她的用户名是什么，但她的在线足迹是什么？

```
kali@kali:~/sherlock$ **python3 sherlock charlottedewittemusic**
[*] Checking username charlottedewittemusic on:
[+] Facebook: [https://www.facebook.com/charlottedewittemusic](https://www.facebook.com/charlottedewittemusic)
[+] Instagram: [https://www.instagram.com/charlottedewittemusic](https://www.instagram.com/charlottedewittemusic)
[+] SoundCloud: [https://soundcloud.com/charlottedewittemusic](https://soundcloud.com/charlottedewittemusic)
[+] Spotify: [https://open.spotify.com/user/charlottedewittemusic](https://open.spotify.com/user/charlottedewittemusic)
[+] Telegram: [https://t.me/charlottedewittemusic](https://t.me/charlottedewittemusic)
[+] TikTok: [https://tiktok.com/@charlottedewittemusic](https://tiktok.com/@charlottedewittemusic)
[+] Twitch: [https://www.twitch.tv/charlottedewittemusic](https://www.twitch.tv/charlottedewittemusic)
[+] VK: [https://vk.com/charlottedewittemusic](https://vk.com/charlottedewittemusic)
[+] YouTube: [https://www.youtube.com/charlottedewittemusic](https://www.youtube.com/charlottedewittemusic)
```

我是说看那个！几秒钟之内，你就可以看到她在脸书、Instagram、SoundCloud、Spotify、Telegram、抖音、Twitch、VK 和 YouTube 上都有账户。

出于兴趣，请搜索您自己的用户名。我敢打赌，你会惊讶于哪些服务你还有账户。

实际上，除了在 Medium 上，我并不仅仅使用我的姓作为用户名，但我很好奇夏洛克是否会找到我的 Medium 账户，而且出于兴趣，还有哪些网站有用户名“ **whittle** ”。

```
kali@kali:~/sherlock$ **python3 sherlock whittle**
[*] Checking username whittle on:
[+] 7Cups: [https://www.7cups.com/@whittle](https://www.7cups.com/@whittle)
[+] 9GAG: [https://www.9gag.com/u/whittle](https://www.9gag.com/u/whittle)
[+] About.me: [https://about.me/whittle](https://about.me/whittle)
[+] Academia.edu: [https://independent.academia.edu/whittle](https://independent.academia.edu/whittle)
[+] Apple Discussions: [https://discussions.apple.com/profile/whittle](https://discussions.apple.com/profile/whittle)
[+] Archive.org: [https://archive.org/details/@whittle](https://archive.org/details/@whittle)
[+] AskFM: [https://ask.fm/whittle](https://ask.fm/whittle)
[+] Audiojungle: [https://audiojungle.net/user/whittle](https://audiojungle.net/user/whittle)
[+] BLIP.fm: [https://blip.fm/whittle](https://blip.fm/whittle)
[+] Badoo: [https://badoo.com/profile/whittle](https://badoo.com/profile/whittle)
[+] Bandcamp: [https://www.bandcamp.com/whittle](https://www.bandcamp.com/whittle)
[+] Behance: [https://www.behance.net/whittle](https://www.behance.net/whittle)
[+] Blogger: [https://whittle.blogspot.com](https://whittle.blogspot.com)
[+] BodyBuilding: [https://bodyspace.bodybuilding.com/whittle](https://bodyspace.bodybuilding.com/whittle)
[+] BuzzFeed: [https://buzzfeed.com/whittle](https://buzzfeed.com/whittle)
[+] CNET: [https://www.cnet.com/profiles/whittle/](https://www.cnet.com/profiles/whittle/)
[+] Career.habr: [https://career.habr.com/whittle](https://career.habr.com/whittle)
[+] Chess: [https://www.chess.com/member/whittle](https://www.chess.com/member/whittle)
[+] Clozemaster: [https://www.clozemaster.com/players/whittle](https://www.clozemaster.com/players/whittle)
[+] Codecademy: [https://www.codecademy.com/profiles/whittle](https://www.codecademy.com/profiles/whittle)
[+] Codepen: [https://codepen.io/whittle](https://codepen.io/whittle)
[+] Codewars: [https://www.codewars.com/users/whittle](https://www.codewars.com/users/whittle)
[+] DailyMotion: [https://www.dailymotion.com/whittle](https://www.dailymotion.com/whittle)
[+] Designspiration: [https://www.designspiration.net/whittle/](https://www.designspiration.net/whittle/)
[+] DeviantART: [https://whittle.deviantart.com](https://whittle.deviantart.com)
[+] Discogs: [https://www.discogs.com/user/whittle](https://www.discogs.com/user/whittle)
[+] Disqus: [https://disqus.com/whittle](https://disqus.com/whittle)
[+] Docker Hub: [https://hub.docker.com/u/whittle/](https://hub.docker.com/u/whittle/)
[+] Dribbble: [https://dribbble.com/whittle](https://dribbble.com/whittle)
[+] Duolingo: [https://www.duolingo.com/profile/whittle](https://www.duolingo.com/profile/whittle)
[+] Ebay: [https://www.ebay.com/usr/whittle](https://www.ebay.com/usr/whittle)
[+] Ello: [https://ello.co/whittle](https://ello.co/whittle)
[+] Etsy: [https://www.etsy.com/shop/whittle](https://www.etsy.com/shop/whittle)
[+] Euw: [https://euw.op.gg/summoner/userName=whittle](https://euw.op.gg/summoner/userName=whittle)
[+] EyeEm: [https://www.eyeem.com/u/whittle](https://www.eyeem.com/u/whittle)
[+] Facebook: [https://www.facebook.com/whittle](https://www.facebook.com/whittle)
[+] Flickr: [https://www.flickr.com/people/whittle](https://www.flickr.com/people/whittle)
[+] Flightradar24: [https://my.flightradar24.com/whittle](https://my.flightradar24.com/whittle)
[+] Flipboard: [https://flipboard.com/@whittle](https://flipboard.com/@whittle)
[+] FortniteTracker: [https://fortnitetracker.com/profile/all/whittle](https://fortnitetracker.com/profile/all/whittle)
[+] Freelancer.com: [https://www.freelancer.com/api/users/0.1/users?usernames%5B%5D=whittle&compact=true](https://www.freelancer.com/api/users/0.1/users?usernames%5B%5D=whittle&compact=true)
[+] Freesound: [https://freesound.org/people/whittle/](https://freesound.org/people/whittle/)
[+] Giphy: [https://giphy.com/whittle](https://giphy.com/whittle)
[+] GitHub: [https://www.github.com/whittle](https://www.github.com/whittle)
[+] HackerNews: [https://news.ycombinator.com/user?id=whittle](https://news.ycombinator.com/user?id=whittle)
[+] HackerRank: [https://hackerrank.com/whittle](https://hackerrank.com/whittle)
[+] House-Mixes.com: [https://www.house-mixes.com/profile/whittle](https://www.house-mixes.com/profile/whittle)
[+] Houzz: [https://houzz.com/user/whittle](https://houzz.com/user/whittle)
[+] IFTTT: [https://www.ifttt.com/p/whittle](https://www.ifttt.com/p/whittle)
[+] Imgur: [https://imgur.com/user/whittle](https://imgur.com/user/whittle)
[+] Instagram: [https://www.instagram.com/whittle](https://www.instagram.com/whittle)
[+] Instructables: [https://www.instructables.com/member/whittle](https://www.instructables.com/member/whittle)
[+] Issuu: [https://issuu.com/whittle](https://issuu.com/whittle)
[+] Itch.io: [https://whittle.itch.io/](https://whittle.itch.io/)
[+] Kaggle: [https://www.kaggle.com/whittle](https://www.kaggle.com/whittle)
[+] Keybase: [https://keybase.io/whittle](https://keybase.io/whittle)
[+] Kik: [https://kik.me/whittle](https://kik.me/whittle)
[+] Kongregate: [https://www.kongregate.com/accounts/whittle](https://www.kongregate.com/accounts/whittle)
[+] Launchpad: [https://launchpad.net/~whittle](https://launchpad.net/~whittle)
[+] LeetCode: [https://leetcode.com/whittle](https://leetcode.com/whittle)
[+] Lichess: [https://lichess.org/@/whittle](https://lichess.org/@/whittle)
**[+] Medium:** [**https://medium.com/@whittle**](https://medium.com/@whittle)
[+] Memrise: [https://www.memrise.com/user/whittle/](https://www.memrise.com/user/whittle/)
[+] MixCloud: [https://www.mixcloud.com/whittle/](https://www.mixcloud.com/whittle/)
[+] MyAnimeList: [https://myanimelist.net/profile/whittle](https://myanimelist.net/profile/whittle)
[+] MyMiniFactory: [https://www.myminifactory.com/users/whittle](https://www.myminifactory.com/users/whittle)
[+] Myspace: [https://myspace.com/whittle](https://myspace.com/whittle)
[+] NameMC (Minecraft.net skins): [https://namemc.com/profile/whittle](https://namemc.com/profile/whittle)
[+] Newgrounds: [https://whittle.newgrounds.com](https://whittle.newgrounds.com)
[+] PCPartPicker: [https://pcpartpicker.com/user/whittle](https://pcpartpicker.com/user/whittle)
[+] Patreon: [https://www.patreon.com/whittle](https://www.patreon.com/whittle)
[+] Periscope: [https://www.periscope.tv/whittle/](https://www.periscope.tv/whittle/)
[+] Pinkbike: [https://www.pinkbike.com/u/whittle/](https://www.pinkbike.com/u/whittle/)
[+] Pinterest: [https://www.pinterest.com/whittle/](https://www.pinterest.com/whittle/)
[+] Pokemon Showdown: [https://pokemonshowdown.com/users/whittle](https://pokemonshowdown.com/users/whittle)
[+] Quora: [https://www.quora.com/profile/whittle](https://www.quora.com/profile/whittle)
[+] Raidforums: [https://raidforums.com/User-whittle](https://raidforums.com/User-whittle)
[+] Reddit: [https://www.reddit.com/user/whittle](https://www.reddit.com/user/whittle)
[+] Roblox: [https://www.roblox.com/user.aspx?username=whittle](https://www.roblox.com/user.aspx?username=whittle)
[+] RubyGems: [https://rubygems.org/profiles/whittle](https://rubygems.org/profiles/whittle)
[+] Scratch: [https://scratch.mit.edu/users/whittle](https://scratch.mit.edu/users/whittle)
[+] Scribd: [https://www.scribd.com/whittle](https://www.scribd.com/whittle)
[+] Slack: [https://whittle.slack.com](https://whittle.slack.com)
[+] SlideShare: [https://slideshare.net/whittle](https://slideshare.net/whittle)
[+] Smule: [https://www.smule.com/whittle](https://www.smule.com/whittle)
[+] SoundCloud: [https://soundcloud.com/whittle](https://soundcloud.com/whittle)
[+] SourceForge: [https://sourceforge.net/u/whittle](https://sourceforge.net/u/whittle)
[+] Spotify: [https://open.spotify.com/user/whittle](https://open.spotify.com/user/whittle)
[+] Star Citizen: [https://robertsspaceindustries.com/citizens/whittle](https://robertsspaceindustries.com/citizens/whittle)
[+] Steam: [https://steamcommunity.com/id/whittle](https://steamcommunity.com/id/whittle)
[+] SteamGroup: [https://steamcommunity.com/groups/whittle](https://steamcommunity.com/groups/whittle)
[+] Steamid: [https://steamid.uk/profile/whittle](https://steamid.uk/profile/whittle)
[+] Strava: [https://www.strava.com/athletes/whittle](https://www.strava.com/athletes/whittle)
[+] Telegram: [https://t.me/whittle](https://t.me/whittle)
[+] Tellonym.me: [https://tellonym.me/whittle](https://tellonym.me/whittle)
[+] TikTok: [https://tiktok.com/@whittle](https://tiktok.com/@whittle)
[+] TrackmaniaLadder: [http://en.tm-ladder.com/whittle_rech.php](http://en.tm-ladder.com/whittle_rech.php)
[+] TradingView: [https://www.tradingview.com/u/whittle/](https://www.tradingview.com/u/whittle/)
[+] Trakt: [https://www.trakt.tv/users/whittle](https://www.trakt.tv/users/whittle)
[+] Trello: [https://trello.com/whittle](https://trello.com/whittle)
[+] TripAdvisor: [https://tripadvisor.com/members/whittle](https://tripadvisor.com/members/whittle)
[+] Twitch: [https://www.twitch.tv/whittle](https://www.twitch.tv/whittle)
[+] Twitter: [https://mobile.twitter.com/whittle](https://mobile.twitter.com/whittle)
[+] Ultimate-Guitar: [https://ultimate-guitar.com/u/whittle](https://ultimate-guitar.com/u/whittle)
[+] Unsplash: [https://unsplash.com/@whittle](https://unsplash.com/@whittle)
[+] VK: [https://vk.com/whittle](https://vk.com/whittle)
[+] VSCO: [https://vsco.co/whittle](https://vsco.co/whittle)
[+] Venmo: [https://venmo.com/whittle](https://venmo.com/whittle)
[+] Vimeo: [https://vimeo.com/whittle](https://vimeo.com/whittle)
[+] Wattpad: [https://www.wattpad.com/user/whittle](https://www.wattpad.com/user/whittle)
[+] We Heart It: [https://weheartit.com/whittle](https://weheartit.com/whittle)
[+] Wikidot: [http://www.wikidot.com/user:info/whittle](http://www.wikidot.com/user:info/whittle)
[+] Windy: [https://community.windy.com/user/whittle](https://community.windy.com/user/whittle)
[+] WordPress: [https://whittle.wordpress.com/](https://whittle.wordpress.com/)
[+] WordPressOrg: [https://profiles.wordpress.org/whittle/](https://profiles.wordpress.org/whittle/)
[+] Xbox Gamertag: [https://xboxgamertag.com/search/whittle](https://xboxgamertag.com/search/whittle)
[+] Zhihu: [https://www.zhihu.com/people/whittle](https://www.zhihu.com/people/whittle)
[+] aminoapp: [https://aminoapps.com/u/whittle](https://aminoapps.com/u/whittle)
[+] authorSTREAM: [http://www.authorstream.com/whittle/](http://www.authorstream.com/whittle/)
[+] couchsurfing: [https://www.couchsurfing.com/people/whittle](https://www.couchsurfing.com/people/whittle)
[+] drive2: [https://www.drive2.ru/users/whittle](https://www.drive2.ru/users/whittle)
[+] fixya: [https://www.fixya.com/users/whittle](https://www.fixya.com/users/whittle)
[+] geocaching: [https://www.geocaching.com/p/default.aspx?u=whittle](https://www.geocaching.com/p/default.aspx?u=whittle)
[+] hunting: [https://www.hunting.ru/forum/members/?username=whittle](https://www.hunting.ru/forum/members/?username=whittle)
[+] last.fm: [https://last.fm/user/whittle](https://last.fm/user/whittle)
[+] moikrug: [https://moikrug.ru/whittle](https://moikrug.ru/whittle)
[+] osu!: [https://osu.ppy.sh/users/whittle](https://osu.ppy.sh/users/whittle)
```

如你所见，它找到了我的中等帐户。现在这里需要注意的是，仅仅因为你搜索一个用户名并有结果，并不意味着它就是上面所展示的同一个人。几乎可以肯定，所有这些“惠特尔”用户名都不是一个人。

您可以使用相同的过程来追踪您的黑客，并查看用户名在其他地方被使用。如果你幸运的话，他们可能会草率地附上一张照片、个人信息或者可以证明他们身份的联系方式。

您也可以向夏洛克提供一个用户名列表，以便逐一检查。例如，如果你正在寻找一个名为" **johndoe** 的用户，你可能想这样做…

```
kali@kali:~/sherlock$ **python3 sherlock johndoe jdoe johnd**
```

我希望你觉得这篇文章有趣并且有用。如果您想随时了解情况，请不要忘记关注我，注册我的[电子邮件通知](https://whittle.medium.com/subscribe)。

# 迈克尔·惠特尔

*   ***如果你喜欢这个，请*** [***跟我上媒***](https://whittle.medium.com/)
*   ***更多有趣的文章，请*** [***关注我的刊物***](https://medium.com/trading-data-analysis)
*   ***有兴趣合作吗？*** [***让我们在 LinkedIn 上连线***](https://www.linkedin.com/in/miwhittle/)
*   ***支持我和其他媒体作者*** [***在此报名***](https://whittle.medium.com/membership)
*   ***请别忘了为文章鼓掌:)←谢谢！***