### Javascript常用总结
> Ajax 封装

```javascript
	
	/**
	 * 一、1.1 封裝AJAX函數
	 * @param {*} option 必须，请求参数
	 * option
	 * ajaxType:必须，请求类型，GET/POST
	 * urlStr:必须，接口URL
	 * ajaxData:必须，GET时候为null,POST的时候为Object{ key:value }
	 * @param {*} success 必须，成功回调函数
	 * @param {*} error 必须，失败回调函数
	 */

	function nativeAjax(option, success, error) {
	    // 定义domain,方便环境切换
	    /*
	     *window.location.host在IE中有兼容性
	     */
	    var domain = 'https://' + window.location.host + '/';
	    // var domain='http://' + window.location.host + '/';
	    var url = domain + option.urlStr;
	    var type = option.ajaxType;
	    var data = option.ajaxData;
	    var xhrRequest = null;
	    if (window.XMLHttpRequest) {
	        xhrRequest = new XMLHttpRequest();
	    } else {
	        xhrRequest = new ActiveXObject('Microsoft.XMLHTTP')
	    }
	    var str = null;
	    xhrRequest.open(type, url, true);
	    if (type === "POST" && data != null) {
	        xhrRequest.setRequestHeader("Content-type", "application/x-www-form-urlencoded;charset=utf-8");
	        for (var key in data) {
	            str += '&' + key + '=' + data[key];
	            str = str.slice(1);
	        }
	    }
	    xhrRequest.onreadystatechange = function() {
	        if (xhrRequest.readyState == 4) {
	            if (xhrRequest.status == 200) {
	                // 1.1、格式化返回的数据
	                var responseData = JSON.parse(xhrRequest.responseText);
	                // 1.2、这里操作数据--------
	                success(responseData);
	            } else {
	                // 1.3、没成功返回HTTP状态码
	                error(xhrRequest.status);
	            }
	        }
	    }
	    xhrRequest.send(str);
	}
	// Example、POST：定義請求參數
	var postOption = {
	    ajaxType: "POST",
	    urlStr: "/info",
	    ajaxData: {
	        "HTTP_USER_TOKEN": token,
	        "HTTP_USER_UID": pfid,
	    }
	}
	nativeAjax(postOption, function(data) {
	    console.log(data);
	}, function(error) {
	    console.log(error);
	});
	//	Example、GET：定义请求参数
	var getOption = {
	    ajaxType: "GET",
	    urlStr: "/good_list",
	    ajaxData: null
	}
	nativeAjax(getOption, function(data) {
	    console.log(data);
	}, function(error) {
	    console.log(error);
	});


	/**
	 * 一、1.2  使用Promise 封裝簡單的Ajax函數
	 *  es6: Promise(resolve,reject) 
	 * 	@param {resolve,reject} 2個參數都是回調函數，
	 * 	resolve 類似：success 回調
	 * 	rejcect 類似：error 回調
	 * 
	 **/

	const Ajax = (method, url, data) => {
	    let xhrRequest = null
	    if (window.XMLHttpRequest) {
	        xhrRequest = new XMLHttpRequest()
	    } else {
	        xhrRequest = new ActiveXObject('Microsoft.XMLHTTP')
	    }
	
	    return new Promise((resolve, reject) => {
	        let str = null
	        xhrRequest.open(method, url, true)
	
	        if (method === "POST" && data != null) {
	            xhrRequest.setRequestHeader("Content-type", "application/x-www-form-urlencoded;charset=utf-8")
	            for (var key in data) {
	                str += '&' + key + '=' + data[key]
	                str = str.slice(1)
	            }
	        }
	
	        xhrRequest.onreadystatechange = function() {
	            if (xhrRequest.readyState == 4) {
	                if (xhrRequest.status == 200) {
	                    resolve(xhrRequest.responseText)
	                } else {
	                    reject(xhrRequest.status)
	                }
	            }
	        }
	
	        xhrRequest.send(str)
	    })
	}
	
	module.exports = Ajax
	
	
	Ajax("GET", "sss/cccas/ddd").then(response => {
	    if (response) {
	        // 成功了
	    }
	}).catch(error => {
	    console.log(error)
	})

```




