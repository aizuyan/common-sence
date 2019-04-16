### 获取当前页面的包名
`adb shell dumpsys window windows | grep "Current"`
### 操作app示例
```
from os import popen
import time as otime
import re

appWidth = 1080
appHeight = 1920

def getAppSize(device):
    cmd = "adb -s {0} shell dumpsys window displays |head -n 3".format(device)
    res = popen(cmd)
    ret = res.read().splitlines()
    res.close()

    pattern     = re.compile(r'(?<=app=)(\d+)x(\d+)')
    size        = pattern.findall(ret[2])
    appWidth    = size[0][0]
    appHeight   = size[0][1]

def swipeLeft(device, start = (), end = (), time = 100):
    if len(start) != 2 or len(end) != 2:
        start = (appWidth * 0.7, appHeight * 0.5)
        end = (appWidth * 0.25, appHeight * 0.45)
    cmd = "adb -s {0} shell input swipe {1} {2} {3} {4} {5}".format(device, start[0], start[1], end[0], end[1], time)
    res = popen(cmd)
    res.close()
    otime.sleep(1)

def swipeRight(device, start = (), end = (), time = 100):
    if len(start) != 2 or len(end) != 2:
        start = (appWidth * 0.25, appHeight * 0.45)
        end = (appWidth * 0.7, appHeight * 0.5)
    cmd = "adb -s {0} shell input swipe {1} {2} {3} {4} {5}".format(device, start[0], start[1], end[0], end[1], time)
    res = popen(cmd)
    res.close()
    otime.sleep(1)

def swipeOneScreen(device):
    cmd = "adb -s {0} shell input swipe {1} {2} {3} {4} {5}".format(device, 521, 1700, 501, 281, 1000)
    res = popen(cmd)
    res.close()
    otime.sleep(1)

def tapPoint(device, point = (0, 0)):
    cmd = "adb -s {0} shell input tap {1} {2}".format(device, point[0], point[1])
    res = popen(cmd)
    res.close()
    otime.sleep(1)

def tapLeft(device):
    tapPoint(device, (272, 998))

def tapRight(device):
    tapPoint(device, (803, 1021))

def quitApp(device):
    cmd = "adb shell input keyevent 4;adb shell input keyevent 4"
    res = popen(cmd)
    res.close()
    otime.sleep(1)

def openApp(device):
    cmd = "adb shell am start -n package/path"
    res = popen(cmd)
    res.close()
    otime.sleep(10)

def stopApp(device):
    cmd = "adb shell am force-stop package"
    res = popen(cmd)
    res.close()
    otime.sleep(10)

def getOne(device):
    swipeLeft(device)
    otime.sleep(5)
    swipeRight(device)
    otime.sleep(2)
    swipeRight(device)
    otime.sleep(2)

times = 0
device = "HMKNW17919012912"
getAppSize(device)


while True:
    if times == 0:
        stopApp(device)
        openApp(device)
    
    times += 1
    tapLeft(device)
    otime.sleep(5)
    getOne(device)
    
    tapRight(device)
    otime.sleep(4)
    getOne(device)
    
    swipeOneScreen(device)
    otime.sleep(2)

    if times >= 100:
        times = 0
```
