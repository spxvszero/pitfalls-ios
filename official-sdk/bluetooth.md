# 蓝牙

## 1.蓝牙设备返回有表情的设备名字时，返回设备名字为空的问题。

蓝牙搜索返回接口：

```
/*!
 *  @method foundPeripheral:advertisementData:
 *
 *  @param peripheral 蓝牙设备对象
 *  @param advertisementData 包含蓝牙设备广播信息或者蓝牙搜索回应数据的字典容器。
 *
 *  @abstract 搜索蓝牙时返回的蓝牙设备信息。
 *
 *  @discussion 请及时保存<i>peripheral</i>引用，系统随时可能将其释放。
 *
 *  @seealso scanStart
 */
-(void)foundPeripheral:(CBPeripheral *)peripheral advertisementData:(NSDictionary *)advertisementData;
```

当返回有表情的设备时：
peripheral返回的name不为空，有设备名字并且带有表情符号。
advertisementData返回的设备名字为空，其他数据正常。
所以，当用设备名字时最好用peripheral的name字段。不要用advertisementData来获取设备名。
