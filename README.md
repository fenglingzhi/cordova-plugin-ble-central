***
####支持：iOS和Android4.3+

#####安装：Cordova

>$ cordova plugin add cordova-plugin-ble-central    https://github.com/don/cordova-plugin-ble-central#phonegap

####PhoneGap
>$ phonegap plugin add cordova-plugin-ble-central    https://github.com/don/cordova-plugin-ble-central#phonegap-build

***
#####API：

#####scan 扫描：

蓝牙扫描的方法,第二个参数10指的扫描时间，单位是秒，device是扫描的设备

>ble.scan(services, seconds, success, failure);

#####参数：
* services: 发现的服务列表或[ ]中所有设备
* seconds: 时间
* success: 成功回调
* failure: 失败回调

######example：
    ble.scan([], 5, function(device) {    
        console.log(JSON.stringify(device));
    }, failure);
***
#####startScan 开始扫描：

>ble.startScan(services, success, failure);

#####参数：
* services: 发现的服务列表或[]中所有设备
* success: 成功回调
* failure: 失败回调

#####example：
    ble.startScan([], function(device) {
        console.log(JSON.stringify(device));
    }, failure);

    setTimeout(ble.stopScan,
         5000,
         function() { console.log("Scan complete"); },
         function() { console.log("stopScan failed"); }
    );
***
#####startScanWithOptions 指定扫描：
>ble.startScanWithOptions(services, options, success, failure);

#####参数：
* services: 发现的服务列表或[]中所有设备
* options: 一个对象指定一组键值对，现在支持的选项如下：
true：如果复制设备应该报道,
false：(默认)如果设备只报告一次。(可选)
* success: 成功回调
* failure: 失败回调

#####example：
    ble.startScan([],
        { reportDuplicates: true }
        function(device) {
            console.log(JSON.stringify(device));
        },
        failure);

    setTimeout(ble.stopScan,
        5000,
        function() { console.log("Scan complete"); },
        function() { console.log("stopScan failed"); }
    );
***
#####stopScan 停止扫描：

>ble.stopScan(success, failure);

#####参数：
* success: 成功回调
* failure: 失败回调

#####example：
    ble.startScan([], function(device) {
        console.log(JSON.stringify(device));
    }, failure);

    setTimeout(ble.stopScan,
        5000,
        function() { console.log("Scan complete"); },
        function() { console.log("stopScan failed"); }
    );

    /* Alternate syntax
    setTimeout(function() {
        ble.stopScan(
            function() { console.log("Scan complete"); },
            function() { console.log("stopScan failed"); }
        );
    }, 5000);
    */
***
#####connect 连接：
连接蓝牙的方法，第一个参数是你扫描到的设备的id，后面的是成功和失败的回调

>ble.connect(device_id, connectSuccess, connectFailure);

#####参数：
* device_id: uuid或者设备的mac地址
* connectSuccess: 连接成功回调
* connectFailure: 连接失败回调

#####example：
    $scope.connectFun=function(device){
        ble.connect(device.id, $scope.onConnected, $scope.onError);
    }
***
#####disconnect 断开连接：

>ble.disconnect(device_id, [success], [failure]);

#####参数：
* device_id: UUID 或者设备 MAC 地址
* success: 成功回调
* failure: 失败回调

#####example：
    ble.disconnect(device.id, function(){
        //do something
    }, function(){　　
        //do something
    });
***
#####read 读：

>ble.read(device_id, service_uuid, characteristic_uuid, success, failure);

#####参数：
* device_id: 设备UUID 或者 设备 MAC 地址
* service_uuid: 蓝牙设备的uuid
* characteristic_uuid: UUID of the BLE characteristic
* success: Success callback function that is invoked when the connection is successful. [optional]
* failure: Error callback function, invoked when error occurs. [optional]

#####example：
    readCounter = setInterval(function () {
        ble.read(device.id, $scope.serviceUUID, $scope.counterCharacteristic,
    $scope.onDataReader, $scope.onReadError);}, 1000);

* $scope.serviceUUID：蓝牙的UUID，具体不懂，每个蓝牙都有，但是这个值需要注意的地方就是它在android和ios上的写法不一样，比如在android上它的值是xxxxfff0-xxxx-xxxx-xxxx-xxxxxxxxxxx,那么它在ios上就是FFF0，这个可以用ionic.Platform.isAndroid()进行平台判断

* $scope.counterCharacteristic：蓝牙的特性值，写法跟UUID类似，在android和ios的差异写法也跟UUID一样，刚开始我在android上写好功能后在iOS上连不上蓝牙问题就出在了这里

* $scope.onDataReader：成功的回调，可以进行读取数据

* $scope.onReadError：失败的回调
***
#####读取数据扩充：

    $scope.onDataReader=function(buffer){
    　　//buffer就是蓝牙读取的数据，但是需要转换才能被引用
    　　var data = new Uint8Array(buffer);//Uint8Array对象：8 位无符号整数值的类型化数组。内容将初始化为0。如果无法分配请求数目的字节，则将引发异常。
    　　//这里可以一步步打印data然后按需要转出所需的数据
    　　//将值赋值给页面上绑定的变量时，如果变量没有变化，试着用
    　　$scope.$apply(function(){
            //将计算后的数据给变量赋值，要用$apply涉及到了ng的脏值检查机制，有兴趣可以去搜搜相关资料
    　　})
    }
***
#####write 写：

>ble.write(device_id, service_uuid, characteristic_uuid, data, success, failure);

#####参数：
* device_id: 设备蓝牙的UUID或设备的mac地址
* service_uuid: 蓝牙服务的UUID
* characteristic_uuid: 具有特征值的蓝牙的UUID
* data: 利用数组存储的二进制数
* success: 成功回调
* failure: 失败回调

#####example：
    // send 1 byte to switch a light on
    var data = new Uint8Array(1);
    data[0] = 1;
    ble.write(device_id, "FF10", "FF11", data.buffer, success, failure);

    // send a 3 byte value with RGB color
    var data = new Uint8Array(3);
    data[0] = 0xFF; // red
    data[1] = 0x00; // green
    data[2] = 0xFF; // blue
    ble.write(device_id, "ccc0", "ccc1", data.buffer, success, failure);

    // send a 32 bit integer
    var data = new Uint32Array(1);
    data[0] = counterInput.value;
    ble.write(device_id, SERVICE, CHARACTERISTIC, data.buffer, success, failure);
***
#####writeWithoutResponse 无响应的写入：
向特征设备写入无返回响应的数据
>ble.writeWithoutResponse(device_id, service_uuid, characteristic_uuid, data, success, failure);

#####参数：
* device_id: 设备蓝牙的UUID或设备的mac地址
* service_uuid: 蓝牙服务的UUID
* characteristic_uuid: 具有特征值的蓝牙的UUID
* data: 利用数组存储的二进制数
* success: 成功回调
* failure: 失败回调

#####startNotification 开始通知：

>ble.startNotification(device_id, service_uuid, characteristic_uuid, success, failure);

#####参数：
* device_id: 设备蓝牙的UUID或设备的mac地址
* service_uuid: 蓝牙服务的UUID
* characteristic_uuid: 具有特征值的蓝牙的UUID
* data: 利用数组存储的二进制数
* success: 成功回调
* failure: 失败回调

>ble.startNotification(device_id, "FFE0", "FFE1", onData, failure);

#####example：
    var onData = function(buffer) {
        // Decode the ArrayBuffer into a typed Array based on the data you expect
        var data = new Uint8Array(buffer);
        alert("Button state changed to " + data[0]);
    }


***
#####stopNotification 停止通知：

>ble.stopNotification(divece_id,servece_uuid,characteristic_uuid,success,failure)

* device_id: 设备蓝牙的UUID或设备的mac地址
* service_uuid: 蓝牙服务的UUID
* characteristic_uuid: 具有特征值的蓝牙的UUID
* data: 利用数组存储的二进制数
* success: 成功回调
* failure: 失败回调
***
#####isConnected 是否连接：

>ble.isConnected(device_id, success, failure);

#####参数：
* device_id: 设备的uuid或者mac地址
* success: 成功回调
* failure: 失败回调

#####example：
    ble.isConnected(
        'FFCA0B09-CB1D-4DC0-A1EF-31AFD3EDFB53',
        function() {
        console.log("Peripheral is connected");
        },
        function() {
            console.log("Peripheral is *not* connected");
        }
    );
***
#####isEnabled 是否开启蓝牙：

>ble.isEnabled(success, failure);

#####参数：
* success: 成功回调
* failure: 失败回调

#####example：
    ble.isEnabled(
        function() {
            console.log("Bluetooth is enabled");
        },
        function() {
            console.log("Bluetooth is *not* enabled");
        }
    );
***
#####startStateNotification 打开通知状态：

>ble.startStateNotifications(success, failure);

#####状态：
* "on"
* "off"
* "turningOn" (Android Only)
* "turningOff" (Android Only)
* "unknown" (iOS Only)
* "resetting" (iOS Only)
* "unsupported" (iOS Only)
* "unauthorized" (iOS Only)

#####参数：
* success: 成功回调
* failure: 失败回调

[](https://github.com/don/cordova-plugin-ble-central#quick-example-8)

#####example：
    ble.startStateNotifications(
        function(state) {
            console.log("Bluetooth is " + state);
        }
    );
***
#####stopStateNotification 停止状态通知：

>ble.startStateNotifications(success, failure);

#####showBluetoothSettings 显示蓝牙设置：

>ble.showBluetoothSettings(success, failure);

#####enable 打开蓝牙：
enable只能在Android平台使用，iOS无法使用。如果蓝牙已经打开，成功回调函数无法调用
>ble.enable(success, failure);

#####参数：
* success: 成功回调
* failure: 失败回调

#####example：
    ble.enable(
        function() {
            console.log("Bluetooth is enabled");
        },
        function() {
            console.log("The user did *not* enable Bluetooth");
        }
    );
(如需转载请注明)