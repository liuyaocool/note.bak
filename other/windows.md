# Win7

## 禁用win按键打开菜单

1.   Win + R , 输入 regedit
2. 在注册表编辑器中, 导航到以下路径: `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Keyboard Layout` 
3. 创建禁用Win键的项: Keyboard Layout 键下右键，选择 New -> Binary Value。
4. 将新创建的二进制值命名为 Scancode Map。
5. 双击 Scancode Map，进入编辑模式。在编辑框中输入以下16进制数值：
    - ` 00000000 00000000 03000000 00005BE0 00000000 `  这个值的含义是将左Win键映射为一个不存在的键码（0xE0）, 从而禁用它.
    - ` 00000000 00000000 03000000 00005CE0 00000000 `  右Win键可以通过此值禁用
6. 保存并重启：

## 程序列表

- [chrome 支持win7](files/chrome.exe)
- [win7破解程序kmspico](files/kmspico.exe)
- [.net 4](files/dnet4.exe)
- [百度拼音3.1](files/BaiduPinyin3.1.exe)