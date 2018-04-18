<template>
  <div id="app">
    <router-view></router-view>
  </div>
</template>

<script>
    var https = require('https');
    var fs=require('fs')
    var locV;
    var onlineV;
  export default {
    name: 'h3demo',
      created(){
          fs.readFile('package.json','utf8',function(err,data){
              data=JSON.parse(data);
              locV=data.version;
          });

          getHttpsData('package.json', function (data) {
              data=JSON.parse(data);
              onlineV=data.version;
              // console.log(locV,onlineV)
              if(compareVersion(locV,onlineV)){
                  data=JSON.stringify(data)
                  // 写入文件
                  // fs.writeFileSync('package.json', data);

                 console.log("需要升级")
              }else {
                  console.log("不需要升级")
              }

          });
      }
  }

    const compareVersion = (v1, v2) => {
      if(v1==undefined||v2==undefined){
          return false;
      }
        const vs1 = v1.toString().split('.'), vs2 = v2.toString().split('.');
        if (vs1.length !== vs2.length) {
            // 版本格式不一致
            return true;
        }
        for (let i = 0; i < vs1.length; ++i) {
            let diff = parseInt(vs2[i], 10) - parseInt(vs1[i], 10);
            if (diff < 0) {
                // vs1 其中一个版本号段小于 vs2
                return false;
            }
            if (diff > 0) {
                // vs2 其中一个版本号段大于 vs1
                return true;
            }
        }
        // 版本一致
        return false;
    };

    var getHttpsData = function (filepath, success, error) {
        // 回调缺省时候的处理
        success = success || function () {};
        error = error || function () {};

        var url = 'https://raw.githubusercontent.com/default-z/h3demo/master/' + filepath + '?r=' + Math.random();

        https.get(url, function (res) {
            var statusCode = res.statusCode;

            if (statusCode !== 200) {
                // 出错回调
                error();
                // 消耗响应数据以释放内存
                res.resume();
                return;
            }

            res.setEncoding('utf8');
            var rawData = '';
            res.on('data', function (chunk) {
                rawData += chunk;
            });

            // 请求结束
            res.on('end', function () {
                // 成功回调
                success(rawData);
            }).on('error', function (e) {
                // 出错回调
                error();
            });
        });
    };
</script>

<style>
  @import "css/app.css";
</style>
