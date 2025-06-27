# Руководство по установке Zapret на Linux

Это пошаговое руководство поможет вам установить и настроить Zapret на Linux или OpenWRT. Следуйте инструкциям внимательно, чтобы избежать ошибок.

## 1. Установка необходимых пакетов

Для начала установите следующие пакеты:

```bash
curl grep findutils tar sed net-tools dnsutils
```

## 2. Переход в директорию `/tmp`

Чтобы не засорять систему лишними файлами, перейдите в директорию `/tmp`:

```bash
cd /tmp
```

## 3. Загрузка архива Zapret

Скачайте архив Zapret с официального репозитория:

**Для Linux:**

```bash
curl -s https://github.com/bol-van/zapret/tags | grep -oP 'v\d+\.\d+\.\d+' | head -1 | xargs -I {} curl -L -O https://github.com/bol-van/zapret/releases/download/{}/zapret-{}.tar.gz
```

**Для OpenWRT:**

```bash
curl -s https://github.com/bol-van/zapret/tags | grep -oP 'v\d+\.\d+\.\d+' | head -1 | xargs -I {} curl -L -O https://github.com/bol-van/zapret/releases/download/{}/zapret-{}-openwrt-embedded.tar.gz
```

## 4. Распаковка архива

Распакуйте архив с помощью команды `tar`:

**Для Linux:**

```bash
tar -xzf zapret-v*.tar.gz
```

**Для OpenWRT:**

```bash
tar -xzf zapret-v*-openwrt-embedded.tar.gz
```

## 5. Переход в каталог Zapret

Перейдите в каталог с распакованным Zapret:

```bash
cd zapret-v*/
```

## 6. Проверка и установка зависимостей

Убедитесь, что установлены все необходимые пакеты для Zapret:

```bash
sudo sh install_prereq.sh
```

В процессе установки выберите тип межсетевого экрана:

```
Select firewall type:
1: iptables
2: nftables
Your choice (default: nftables): nftables
```

Рекомендуется выбрать `nftables`, так как он обеспечивает лучшую производительность по сравнению с `iptables`.

**Проверка зависимостей:**

- Для `iptables` требуются: `curl`, `ipset`, `iptables`.
- Для `nftables` требуются: `curl`, `nftables`.

Если зависимости отсутствуют, скрипт попытается установить их автоматически. Если установка не удалась, установите их вручную.

Пример вывода, если все зависимости на месте:

```
* checking prerequisites
required utilities exist: curl nft
```

## 7. Установка бинарных файлов

Установите необходимые бинарные файлы в соответствующие директории:

```bash
sudo sh install_bin.sh
```

Пример успешного вывода:

```
using arch detect method: bash
linux-x86_64 is OK
installing binaries ...
linking: ../binaries/linux-x86_64/ip2net => /tmp/zapret-v71.1.1/ip2net
linking: ../binaries/linux-x86_64/mdig => /tmp/zapret-v71.1.1/mdig
linking: ../binaries/linux-x86_64/nfqws => /tmp/zapret-v71.1.1/nfq
linking: ../binaries/linux-x86_64/tpws => /tmp/zapret-v71.1.1/tpws
```

## 8. Установка Zapret

Запустите скрипт установки Zapret:

```bash
sudo sh install_easy.sh
```

В процессе установки следуйте указаниям:

```
easy install is supported only from default location: /opt/zapret
currently its run from /tmp/zapret-v71.1.1
do you want the installer to copy it for you (default: N) (Y/N)? Y
```

Выберите `Y`.

**Выбор типа межсетевого экрана:**

```
select firewall type:
1: iptables
2: nftables
your choice (default: nftables): nftables
```

Выберите `nftables`, как указано ранее.

**Поддержка IPv6:**

```
enable ipv6 support (default: N) (Y/N)? N
```

Выберите `N`, если у вас нет IPv6. Чтобы проверить наличие IPv6, прервите скрипт (Ctrl+Z) и выполните:

```bash
curl -6 -s https://ipinfo.io/ip && echo " - IPv6 доступен" || echo "IPv6 недоступен"
```

Если нужно начать установку заново, удалите временные файлы:

```bash
sudo rm -rf /opt/zapret
```

**Flow offloading:**

```
flow offloading can greatly increase speed on slow devices and high speed links (usually 150+ mbits)
offloading is applicable only to forwarded traffic. it has no effect on outgoing traffic
hardware flow offloading is available only on specific supporting hardware. most likely will not work on a generic system
offloading breaks traffic shaper
select flow offloading:
1: none
2: software
3: hardware
your choice (default: none): none
```

Выберите `none`, так как flow offloading может нарушить работу Zapret.

**Фильтрация:**

```
select filtering:
1: none
2: ipset
3: hostlist
4: autohostlist
your choice (default: none): none
```

Выберите `none`, так как фильтрация будет настроена позже.

**Режимы TPWS:**

```
enable tpws socks mode on port 987? (default: N) (Y/N)? N
```

Выберите `N`.

```
enable tpws transparent mode? (default: N) (Y/N)? N
```

Выберите `N`.

**Режим NFQWS:**

```
enable nfqws? (default: N) (Y/N)? Y
```

Выберите `Y`.

**Редактирование опций:**

```
we have 3 user defined desync profile(s) and default low priority profile 0
Running as UID=999 GID=988
command line parameters verified
do you want to edit the options (default: N) (Y/N)? N
```

Выберите `N`, так как конфигурация будет настроена позже.

**LAN интерфейс:**

```
LAN interface:
1: NONE
2: docker0
3: eth0
4: lo
your choice (default: NONE):
```

Выберите `NONE`, если не уверены в выборе.

**WAN интерфейс:**

```
WAN interface:
1: ANY
2: docker0
3: eth0
4: lo
your choice (default: ANY):
```

Выберите `ANY`, если не знаете, что выбрать.

Чтобы посмотреть доступные интерфейсы, прервите скрипт (Ctrl+Z) и выполните:

```bash
ifconfig
```

Если нужно начать заново, удалите временные файлы:

```bash
sudo rm -rf /opt/zapret
```

Интерфейсы можно настроить позже в файле `/opt/zapret/config`, добавив строки:

```bash
IFACE_LAN=ваш_lan_интерфейс
IFACE_WAN=ваш_wan_интерфейс
```

Для установки интерфейсов по умолчанию (`NONE` и `ANY`) просто удалите эти строки из файла `/opt/zapret/config`.

## 9. Загрузка списков от Flowseal

Скачайте списки блокировки от Flowseal:

```bash
sudo curl -o /opt/zapret/ipset/zapret-hosts-user.txt https://raw.githubusercontent.com/Flowseal/zapret-discord-youtube/refs/heads/main/lists/list-general.txt
sudo curl -o /opt/zapret/ipset/zapret-ip-user.txt https://raw.githubusercontent.com/Flowseal/zapret-discord-youtube/refs/heads/main/lists/ipset-all.txt
```

## 10. Улучшение читаемости конфигурации

Выполните следующие команды для упрощения конфигурационного файла:

```bash
sudo sed -i '/^--filter/d; /^#/d; /^$/d' /opt/zapret/config && sudo sed -i '/^NFQWS_OPT/{s/^NFQWS_OPT=.*/NFQWS_OPT=""/; :a;n;/^"$/!ba;d}' /opt/zapret/config
```

## 11. Настройка портов

Настройте необходимые порты в конфигурации Zapret:

```bash
sudo sed -i '/^NFQWS_PORTS_TCP/ s/.*/NFQWS_PORTS_TCP=80,443,1024-65535/' /opt/zapret/config && sudo sed -i '/^NFQWS_PORTS_UDP/ s/.*/NFQWS_PORTS_UDP=443,50000-50100,1024-65535/' /opt/zapret/config
```

## 12. Выбор стратегии Zapret

Вы можете использовать готовые стратегии от Flowseal, адаптированные для Linux. Выполните следующую команду, заменив `URL` на ссылку на нужную стратегию в формате RAW:

```bash
sudo sed -i "s#^NFQWS_OPT=\".*#NFQWS_OPT=\" $(curl -sL 'URL' | xargs)\"#" /opt/zapret/config
```

### Доступные стратегии

#### С ipset

- [general](https://raw.githubusercontent.com/vayulqq/Installing_Zapret_on_Linux/refs/heads/main/Strategies/with_ipset/general)
- [general (ALT)](https://raw.githubusercontent.com/vayulqq/Installing_Zapret_on_Linux/refs/heads/main/Strategies/with_ipset/general%20%28ALT%29)
- [general (ALT2)](https://raw.githubusercontent.com/vayulqq/Installing_Zapret_on_Linux/refs/heads/main/Strategies/with_ipset/general%20%28ALT2%29)
- [general (ALT3)](https://raw.githubusercontent.com/vayulqq/Installing_Zapret_on_Linux/refs/heads/main/Strategies/with_ipset/general%20%28ALT3%29)
- [general (ALT4)](https://raw.githubusercontent.com/vayulqq/Installing_Zapret_on_Linux/refs/heads/main/Strategies/with_ipset/general%20%28ALT4%29)
- [general (ALT5)](https://raw.githubusercontent.com/vayulqq/Installing_Zapret_on_Linux/refs/heads/main/Strategies/with_ipset/general%20%28ALT5%29)
- [general (ALT6)](https://raw.githubusercontent.com/vayulqq/Installing_Zapret_on_Linux/refs/heads/main/Strategies/with_ipset/general%20%28ALT6%29)
- [general (FAKE TLS ALT)](https://raw.githubusercontent.com/vayulqq/Installing_Zapret_on_Linux/refs/heads/main/Strategies/with_ipset/general%20%28FAKE%20TLS%20ALT%29)
- [general (FAKE TLS AUTO ALT)](https://raw.githubusercontent.com/vayulqq/Installing_Zapret_on_Linux/refs/heads/main/Strategies/with_ipset/general%20%28FAKE%20TLS%20AUTO%20ALT%29)
- [general (FAKE TLS AUTO ALT2)](https://raw.githubusercontent.com/vayulqq/Installing_Zapret_on_Linux/refs/heads/main/Strategies/with_ipset/general%20%28FAKE%20TLS%20AUTO%20ALT2%29)
- [general (FAKE TLS AUTO)](https://raw.githubusercontent.com/vayulqq/Installing_Zapret_on_Linux/refs/heads/main/Strategies/with_ipset/general%20%28FAKE%20TLS%20AUTO%29)
- [general (FAKE TLS)](https://raw.githubusercontent.com/vayulqq/Installing_Zapret_on_Linux/refs/heads/main/Strategies/with_ipset/general%20%28FAKE%20TLS%29)
- [general (МГТС)](https://raw.githubusercontent.com/vayulqq/Installing_Zapret_on_Linux/refs/heads/main/Strategies/with_ipset/general%20%28%D0%9C%D0%93%D0%A2%D0%A1%29)
- [general (МГТС2)](https://raw.githubusercontent.com/vayulqq/Installing_Zapret_on_Linux/refs/heads/main/Strategies/with_ipset/general%20%28%D0%9C%D0%93%D0%A2%D0%A12%29)

#### Без ipset

- [general](https://raw.githubusercontent.com/vayulqq/Installing_Zapret_on_Linux/refs/heads/main/Strategies/without_ipset/general)
- [general (ALT)](https://raw.githubusercontent.com/vayulqq/Installing_Zapret_on_Linux/refs/heads/main/Strategies/without_ipset/general%20%28ALT%29)
- [general (ALT2)](https://raw.githubusercontent.com/vayulqq/Installing_Zapret_on_Linux/refs/heads/main/Strategies/without_ipset/general%20%28ALT2%29)
- [general (ALT3)](https://raw.githubusercontent.com/vayulqq/Installing_Zapret_on_Linux/refs/heads/main/Strategies/without_ipset/general%20%28ALT3%29)
- [general (ALT4)](https://raw.githubusercontent.com/vayulqq/Installing_Zapret_on_Linux/refs/heads/main/Strategies/without_ipset/general%20%28ALT4%29)
- [general (ALT5)](https://raw.githubusercontent.com/vayulqq/Installing_Zapret_on_Linux/refs/heads/main/Strategies/without_ipset/general%20%28ALT5%29)
- [general (ALT6)](https://raw.githubusercontent.com/vayulqq/Installing_Zapret_on_Linux/refs/heads/main/Strategies/without_ipset/general%20%28ALT6%29)
- [general (FAKE TLS ALT)](https://raw.githubusercontent.com/vayulqq/Installing_Zapret_on_Linux/refs/heads/main/Strategies/without_ipset/general%20%28FAKE%20TLS%20ALT%29)
- [general (FAKE TLS AUTO ALT)](https://raw.githubusercontent.com/vayulqq/Installing_Zapret_on_Linux/refs/heads/main/Strategies/without_ipset/general%20%28FAKE%20TLS%20AUTO%20ALT%29)
- [general (FAKE TLS AUTO ALT2)](https://raw.githubusercontent.com/vayulqq/Installing_Zapret_on_Linux/refs/heads/main/Strategies/without_ipset/general%20%28FAKE%20TLS%20AUTO%20ALT2%29)
- [general (FAKE TLS AUTO)](https://raw.githubusercontent.com/vayulqq/Installing_Zapret_on_Linux/refs/heads/main/Strategies/without_ipset/general%20%28FAKE%20TLS%20AUTO%29)
- [general (FAKE TLS)](https://raw.githubusercontent.com/vayulqq/Installing_Zapret_on_Linux/refs/heads/main/Strategies/without_ipset/general%20%28FAKE%20TLS%29)
- [general (МГТС)](https://raw.githubusercontent.com/vayulqq/Installing_Zapret_on_Linux/refs/heads/main/Strategies/without_ipset/general%20%28%D0%9C%D0%93%D0%A2%D0%A1%29)
- [general (МГТС2)](https://raw.githubusercontent.com/vayulqq/Installing_Zapret_on_Linux/refs/heads/main/Strategies/without_ipset/general%20%28%D0%9C%D0%93%D0%A2%D0%A12%29)
Доки пока не закончил)
