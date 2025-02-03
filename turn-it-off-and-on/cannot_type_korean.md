# 한글 입력이 안 될 때

우분투 데스크탑에서 가끔 한글 입력이 잘 안 될 때가 있다.

## 재시작

```bash
ps aux | grep ibus

2234 sh -c /usr/bin/ibus-daemon --panel disable $([ "$XDG_SESSION_TYPE" = "x11" ] && echo "--xim")
2239 /usr/bin/ibus-daemon --panel disable
2318 /usr/libexec/ibus-dconf
2322 /usr/libexec/ibus-extension-gtk3
2335 /usr/libexec/ibus-portal
2370 /usr/libexec/ibus-engine-hangul --ibus
4556 /usr/libexec/ibus-x11
```

ibus-daemon을 재시작한다.

```bash
ibus-daemon -drx
```

```bash
ps aux | grep ibus

78976 ibus-daemon -drx
79006 /usr/libexec/ibus-dconf
79010 /usr/libexec/ibus-ui-gtk3
79029 /usr/libexec/ibus-extension-gtk3
79031 /usr/libexec/ibus-x11 --kill-daemon
79037 /usr/libexec/ibus-portal
79162 /usr/libexec/ibus-engine-hangul --ibus
```

