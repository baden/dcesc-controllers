# dcesc-controllers

Тут буде і плата і програма.



## Так. Шось в процесі ми якось дішли до потужної плати. І назва трохи не відповідає.

## Мотори

Експериментую з моторами з редуктором.

Починаю експерімент з [JGA25-370](https://www.aliexpress.com/item/1005006961499514.html)
та [GA24Y-370](https://www.aliexpress.com/item/1005005835525169.html)

на двигунах діаметром 24.4мм

Ціль:
напруга живлення - 24В
струм - 3А
керування - RS485

Хотілось би CAN, але поки не підібрани варіанти конролера.

Пропонується варіанти драйвера двигуна:

- [DRV8870DDAR](https://www.kosmodrom.ua/mikroshema-kontrolera-dviguna/drv8870ddar-4.html) - 12грн
- [DRV8874PWPR](https://www.kosmodrom.ua/mikroshema-kontrolera-dviguna/drv8874pwpr-2.html) - 44грн


# Робимо цю версію на готових драйверах. Бо два-три мотори на одному мікроконтроллері то дуже складно.


## На майбутньє, може буду підбирати якісь китайські компоненти, бо ціна на амеріканських брендах виходить якась зовсім неадекватна.






## Встановлення інструментів

https://github.com/riscv-software-src/homebrew-riscv

```bash
brew tap riscv-software-src/riscv
brew install riscv-tools
brew install riscv-openocd
brew test riscv-tools
/opt/homebrew/opt/riscv-gnu-toolchain/bin/riscv64-unknown-elf-gcc --version
```

/opt/homebrew/opt/riscv-openocd/bin/openocd



## Обираємо мікроконтролер

Я ще не визначився, чи треба робити виключно на CAN, чи може обійтись RS485.

- Оцей мабуть ідеальний для контролеру двигуна
https://www.kosmodrom.ua/mikrokontroler/stm32g431cbt6.html

Для плат с готовий драйвером двигуна, контролер:

https://www.kosmodrom.ua/mikrokontroler/stm32f042f6p6.html

- Шось мегапопулярне, але не впевнен шо воно.
https://www.kosmodrom.ua/mikrokontroler/stm32f103c8t6.html

- Ексотіка для ізвращьєнців
https://www.kosmodrom.ua/mikrokontroler/gd32f103cbt6-2.html

Ще подивлюсь на цей: CH32V203. Здається є сумісний по ногам як STM, так і GD, так і AT

## Обираємо транзистор на потужну плату

### 60 V

https://www.kosmodrom.ua/tranzistor-poloviy-mosfet/60n06-2.html, 39 nC, 14mOhm
https://www.kosmodrom.ua/tranzistor-poloviy-mosfet/csd18537nq5a-2.html, 18 nC, 13mOhm
https://www.kosmodrom.ua/tranzistor-poloviy-mosfet/aon7262e-2.html, 45 nC, 6.2mOhm


## Драйвер MOSFET


https://www.kosmodrom.ua/drayveri-igbt-mosfet/eg3013s-2.html, 0.8/1.0A,


## Драйвер CAN-шини.

Треба або взяти 3.3В CAN, ти живити окремим живленням 5V, та обирати з Vio, чи согласовувати рівні.

https://www.kosmodrom.ua/interfeys-can-2/sn65hvd230dr.html
https://www.kosmodrom.ua/interfeys-can-2/sn65hvd232d.html (no stb and sleep)

## Готовий контролер на слабкіший мотор

https://www.kosmodrom.ua/mikroshema-kontrolera-dviguna/drv8870ddar-4.html, 3.6А, 45V
https://www.kosmodrom.ua/mikroshema-kontrolera-dviguna/drv8870ddar-2.html,
https://www.kosmodrom.ua/mikroshema-kontrolera-dviguna/drv8874pwpr-2.html, 6А, 37V


## Всіляке різне

https://www.kosmodrom.ua/tranzistor-poloviy-mosfet/ao4828.html, дешевий сдвоєний мосфет на драйвер до 3А.

https://www.kosmodrom.ua/stabilizator-naprugi/ht7533s-umw-2.html, Стабілізатор напруги на процесор, 3.3V, 0.1A



## Во, подивиться це

https://github.com/wagiminator/CH32V003-GameConsole/tree/main

Ще це:
https://github.com/wagiminator/MCU-Templates

Ще тут купа посилань:
https://github.com/wagiminator/Development-Boards/tree/main/CH32V003A4M6_DevBoard


https://github.com/AlessandroAU/ESP32-CRSF-Packet-Gen/blob/master/CRSF_testcode/CRSF.h
https://px4.github.io/Firmware-Doxygen/d9/dd2/crsf_8cpp_source.html

https://github.com/vedderb/bldc_uart_comm_stm32f4_discovery/blob/c19a6283e187040f25aa52e213673938db054d06/bldc_interface.c#L567

https://vedder.se/2015/10/communicating-with-the-vesc-using-uart/

