# 实用代码片段
## 索引
- [时间差](#时间差)
- [监测数组包含](#监测数组包含)
- [cookie](#cookie)

## 时间差
##### [回到索引](#索引)
``` javascript
    /*
     * 时间差，支持单位：[毫秒] [秒]
     * @param {timestamp} [String] 以毫秒为单位的 UNIX 纪元方式，开始于 1970-01-01
     */
    function filterTime(timestamp) {
      let time = timestamp;

      if (!time) return '';

      // 如果 timestamp 为 10 位数字，则单位为秒
      if (time.toString().length === 10) {
        time = timestamp * 1000
      };

      // 测试时间戳: 1495159106281 => 2017/5/19 9:58
      // 正式时间戳: new Date(time).getTime()
      let creaTime = new Date(time).getTime(),  // 创建时间
          curTime = new Date().getTime(),         // 当前时间
          $Date = new Date(time),
          year = $Date.getFullYear(),    // 年
          month = $Date.getMonth() + 1,  // 月
          date = $Date.getDate(),        // 日
          diffTime = curTime - creaTime,                // 毫秒差
          diffSecounds = Math.floor(diffTime / 1000),   // 秒差
          diffMinutes = Math.floor(diffSecounds / 60),  // 分钟差
          diffHours = Math.floor(diffMinutes / 60),     // 小时差
          diffDays = Math.floor(diffHours / 24);        // 天差

      if (diffMinutes === 0) {
        return diffSecounds + '秒前'
      }
      if (diffHours === 0) {
        return diffMinutes + '分钟前'
      }
      if (diffDays === 0) {
        return diffHours + '小时前'
      }
      if (diffDays > 0 && diffDays <= 20) {
        return diffDays + '天前'
      }
      if (diffDays > 20) {
        return year + '-' + month + '-' + date
      }
    }
```

## 监测数组包含
##### [回到索引](#索引)
``` javascript
    /*
     * 监测数组包含
     * @param {arr} [Array]  做检测的数组
     * @param {ele} [String] 做检测的属性
     */
    function contains(arr, ele) {  
        var i = arr.length;  
        while (i--) {  
            if (arr[i] === ele) {  
                return true;  
            }  
        }  
        return false;  
    }   
```


## Cookie
##### [回到索引](#索引)
> 该片段可以直接运用到 vue-cli 项目中。\
若是普通项目，需按照 ``function getCookie(name) {...}`` 写法
``` javascript
    export const setCookie = (name, value, seconds) => {
        seconds = seconds || 0; //seconds有值就直接赋值，没有为0，这个根php不一样。
        let expires = "";
        if (seconds != 0) { //设置cookie生存时间
            let date = new Date();
            date.setTime(date.getTime() + (seconds * 1000));
            expires = "; expires=" + date.toGMTString();
        }
        document.cookie = name + "=" + escape(value) + expires + "; path=/"; //转码并赋值
    }

    export const getCookie = (name) => {
        let arr, reg = new RegExp("(^| )" + name + "=([^;]*)(;|$)");
        if (arr = document.cookie.match(reg))
            return unescape(arr[2]);
        else
            return null;
    }

    export const delCookie = (name) => {
        let exp = new Date();
        exp.setTime(exp.getTime() - 1);
        let cval = getCookie(name);
        if (cval != null)
            document.cookie = name + "=" + cval + ";expires=" + exp.toGMTString();
    }
```