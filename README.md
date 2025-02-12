# Gnome 외 터미널에서 gnome-terminal 사용하기
- **gnome-terminal**은 **dbus**의 의존성이 있어서 단독으로 사용하는 경우 문제가 발생한다. 정상적으로 사용하기 위해서는 **gnome-terminal-server** 서비스를 미리 백그라운드로 실행해야 한다. 이때 **gnome-terminal-server**가 **gnome-terminal**의 요청을 일정 시간 동안 받지 않으면 자동으로 종료되기 때문에 **systemd** 서비스를 이용해야만 한다.
```
$ systemctl enable --user gnome-terminal-server
$ systemctl start --user gnome-terminal-server
```

# Fedora Linux에서 tuned 프로파일 변경
- 데스크탑인 경우 전원 관리 서비스가 활성화 되어 있다. 이때 사용되는 서비스는 **tuned-ppd** 로 전원 설정이 "성능", "균형", "절전" 중 무엇이냐에 따라서 **tuned** 프로파일을 설정한다. **tuned-ppd** 서비스를 중지 하거나 삭제해서 고정적인 프로파일을 설정하든지, 기본값인 "균형"의 프로파일을 수정해야 한다.

case 1) ppd.conf 설정파일 수정
```
# vi /etc/tuned/ppd.conf
[main]
# The default PPD profile
default=balanced
battery_detection=true

[profiles]
# PPD = TuneD
power-saver=powersave
balanced=desktop # balanced -> desktop
performance=throughput-performance

[battery]
# PPD = TuneD
balanced=balanced-battery
```
case 2) tuned-ppd 서비스 중지
```
# systemctl stop tuned-ppd
# systemctl mask tuned-ppd
```
case 3) tuned-ppd 삭제
- (주의) 몇몇 데스크탑 환경은 **tuned-ppd**에 의존성을 가진다.
```
# dnf remove tuned-ppd
```

# Wayland 환경에 키매핑 설정 (한영키 설정)
- [keyd](https://github.com/rvaiya/keyd)
- Fedora 리눅스에서는 copr 저장소를 이용해야 한다.
```
# dnf copr enable dspom/keyd
# dnf install keyd
```
- 설정 파일
```
# vi /etc/keyd/default.conf
[ids]

*

[main]

# 한영키
rightalt = hangeul

# 한자키
rightcontrol = hanja
```
- 서비스 활성화
```
sudo systemctl enable --now keyd
```

# GDM 로고 커스텀마이징
- /etc/dconf/db/gdm.d/01-override-logo
```
[org/gnome/login-screen]
logo='/path/to/logo.png'
```
- 시스템 레벨 데이타베이스를 갱신한다.
```
# dconf update
```

# Wayland 환경에서 Brave 웹 브라우저 메뉴 그림자 문제
- #zone-platform-hint 키를 Wayland로 직접 지정해 줘야 한다.
```
주소줄 -> brave://flags/
Preferred Ozone platform -> Wayland
Brave 웹 브라우저 재시작
``` 
