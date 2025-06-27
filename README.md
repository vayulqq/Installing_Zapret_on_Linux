# Установка Zapret на Linux

1. **Установка необходимых пакетов**  
   Для установки Zapret на Linux необходимо установить следующие пакеты:  
   `curl`, `grep`, `findutils`, `tar`, `sed`, `net-tools`, `dnsutils`.

2. **Загрузка архива с Zapret**  
   После установки пакетов загрузите архив с Zapret из официального репозитория:  
   ```bash
   curl -s https://github.com/bol-van/zapret/tags | grep -oP 'v\d+\.\d+\.\d+' | head -1 | xargs -I {} curl -L -O https://github.com/bol-van/zapret/releases/download/{}/zapret-{}.tar.gz
   ```

   Для OpenWRT:  
   ```bash
   curl -s https://github.com/bol-van/zapret/tags | grep -oP 'v\d+\.\d+\.\d+' | head -1 | xargs -I {} curl -L -O https://github.com/bol-van/zapret/releases/download/{}/zapret-{}-openwrt-embedded.tar.gz
   ```

3. **Распаковка архива**  
   Распакуйте архив с Zapret с помощью команды `tar`:  
   ```bash
   tar -xzf zapret-v*.tar.gz
   ```

   Для OpenWRT:  
   ```bash
   tar -xzf zapret-v*-openwrt-embedded.tar.gz
   ```

4. **Переход в каталог с Zapret**  
   Перейдите в каталог с распакованным Zapret:  
   ```bash
   cd zapret-v*/
   ```

5. **Установка необходимых пакетов для Zapret**  
   Установите или проверьте наличие необходимых пакетов для Zapret:  
   ```bash
   sudo sh install_prereq.sh
   ```

   Выберите тип файрвола:  
   ```
   select firewall type:
   1: iptables
   2: nftables
   your choice (default: nftables): nftables
   ```

   Здесь выбирайте желаемый тип файрвола. Я выбрал `nftables` из-за более высокой скорости по сравнению с `iptables`.  

   Проверка зависимостей:  
   ```
   * checking prerequisites
   required utilities exist: curl nft
   ```

   Если зависимости уже установлены, вы увидите подобное сообщение. Если их нет, они будут установлены автоматически. В случае проблем установите зависимости вручную:  
   - Для `iptables`: `curl`, `ipset`, `iptables`.  
   - Для `nftables`: `curl`, `nftables`.

6. **Установка бинарных файлов Zapret**  
   Установите необходимые бинарные файлы в соответствующие папки:  
   ```bash
   sudo sh install_bin.sh
   ```

   Пример вывода (указывает на успешную установку):  
   ```
   using arch detect method: bash
   linux-x86_64 is OK
   installing binaries ...
   linking: ../binaries/linux-x86_64/ip2net => /tmp/zapret-v71.1.1/ip2net
   linking: ../binaries/linux-x86_64/mdig => /tmp/zapret-v71.1.1/mdig
   linking: ../binaries/linux-x86_64/nfqws => /tmp/zapret-v71.1.1/nfq
   linking: ../binaries/linux-x86_64/tpws => /tmp/zapret-v71.1.1/tpws
   ```

7. **Установка Zapret**  
   Выполните установку Zapret:  
   ```bash
   sudo sh install_easy.sh
   ```

   Вывод команды:  
   ```
   easy install is supported only from default location: /opt/zapret
   currently its run from /tmp/zapret-v71.1.1
   do you want the installer to copy it for you (default: N) (Y/N)? Y
   ```

   Выберите `Y`.  

   Выберите тип файрвола:  
   ```
   select firewall type:
   1: iptables
   2: nftables
   your choice (default: nftables): nftables
   ```

   Здесь снова выберите `nftables` или желаемый тип файрвола.  

   Включение поддержки IPv6:  
   ```
   enable ipv6 support (default: N) (Y/N)? N
   ```

   Выберите `Y` или `N` в зависимости от наличия IPv6. Я выбрал `N`, так как IPv6 у меня отсутствует.  

   Чтобы проверить наличие IPv6, прервите установку с помощью `Ctrl+Z` и выполните:  
   ```bash
   curl -6 -s https://ipinfo.io/ip && echo " - IPv6 доступен" || echo "IPv6 недоступен"
   ```

   Если нужно начать установку заново, выполните:  
   ```bash
   sudo rm -rf /opt/zapret
   ```

   Настройка flow offloading:  
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

   Выбор фильтрации:  
   ```
   select filtering:
   1: none
   2: ipset
   3: hostlist
   4: autohostlist
   your choice (default: none): none
   ```

   Выберите `none`, так как фильтрация будет настроена позже.  

   Настройка режима `tpws` socks:  
   ```
   enable tpws socks mode on port 987? (default: N) (Y/N)? N
   ```

   Выберите `N`.  

   Настройка прозрачного режима `tpws`:  
   ```
   enable tpws transparent mode? (default: N) (Y/N)? N
   ```

   Выберите `N`.  

   Включение `nfqws`:  
   ```
   enable nfqws? (default: N) (Y/N)? Y
   ```

   Выберите `Y`.  

   Проверка параметров:  
   ```
   we have 3 user defined desync profile(s) and default low priority profile 0
   Running as UID=999 GID=988
   command line parameters verified
   do you want to edit the options (default: N) (Y/N)? N
   ```

   Выберите `N`, так как конфигурация будет настроена позже.  

   Выбор LAN-интерфейса:  
   ```
   LAN interface:
   1: NONE
   2: docker0
   3: eth0
   4: lo
   your choice (default: NONE):
   ```

   Если вы не уверены, выберите `NONE`. Zapret будет работать с настройками по умолчанию.  

   Выбор WAN-интерфейса:  
   ```
   WAN interface:
   1: ANY
   2: docker0
   3: eth0
   4: lo
   your choice (default: ANY):
   ```

   Если вы не уверены, выберите `ANY`. Zapret будет работать с настройками по умолчанию.  

   Чтобы просмотреть доступные интерфейсы, прервите скрипт (`Ctrl+Z`) и выполните:  
   ```bash
   ifconfig
   ```

   Если нужно начать установку заново, выполните:  
   ```bash
   sudo rm -rf /opt/zapret
   ```

   Интерфейсы можно изменить позже в файле `/opt/zapret/config`, добавив строки:  
   ```bash
   IFACE_LAN=ваш_lan_интерфейс
   IFACE_WAN=ваш_wan_интерфейс
   ```

   Чтобы вернуть интерфейсы к значениям по умолчанию (`NONE` и `ANY`), удалите эти строки из файла `/opt/zapret/config`.

8. **Скачивание списков от Flowseal**  
   Загрузите списки для Zapret из репозитория Flowseal:  
   ```bash
   sudo curl -o /opt/zapret/ipset/zapret-hosts-user.txt https://raw.githubusercontent.com/Flowseal/zapret-discord-youtube/refs/heads/main/lists/list-general.txt
   sudo curl -o /opt/zapret/ipset/zapret-ip-user.txt https://raw.githubusercontent.com/Flowseal/zapret-discord-youtube/refs/heads/main/lists/ipset-all.txt
   ```

9. **Улучшение читаемости конфигурационного файла**  
   Упростите файл конфигурации:  
   ```bash
   sudo sed -i '/^--filter/d; /^#/d; /^$/d' /opt/zapret/config && sudo sed -i '/^NFQWS_OPT/{s/^NFQWS_OPT=.*/NFQWS_OPT=""/; :a;n;/^"$/!ba;d}' /opt/zapret/config
   ```

10. **Настройка портов в конфигурации Zapret**  
    Настройте необходимые порты в файле конфигурации:  
    ```bash
    sudo sed -i '/^NFQWS_PORTS_TCP/ s/.*/NFQWS_PORTS_TCP=80,443,1024-65535/' /opt/zapret/config && sudo sed -i '/^NFQWS_PORTS_UDP/ s/.*/NFQWS_PORTS_UDP=443,50000-50100,1024-65535/' /opt/zapret/config
    ```

11. **Выбор стратегии для Zapret**  
    Для настройки стратегии используйте готовые конфигурации от Flowseal, адаптированные для Linux. Если вы хотите использовать свои стратегии, пропустите этот шаг.  
    Выполните следующую команду, заменив `URL` на ссылку на нужную стратегию в формате RAW:  
    ```bash
    sudo sed -i "s#^NFQWS_OPT=\".*#NFQWS_OPT=\" $(curl -sL 'URL' | xargs)\"#" /opt/zapret/config
    ```

    **Доступные стратегии**  
    - **С ipset**  
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

    - **Без ipset**  
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
