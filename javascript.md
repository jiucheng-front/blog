### Javascript常用总结
> 一、Ajax 封装

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


> 二、判断是否是IOS

```javascript

	function isIos() {
	    var u = navigator.userAgent,
	        app = navigator.appVersion;
	    //ios终端
	    var isiOS = !!u.match(/\(i[^;]+;( U;)? CPU.+Mac OS X/);
	    return isiOS;
	}

```

> 三、关于原生JS操作className

```javascript

	/**
	 * 1：是否含有某个class,返回：true/false
	 * 2：没有指定的className就追加指定的className
	 * 3：有指定的className就删除指定的className
	 * @param {*} elem 必须，原生DOM 
	 * @param {*} cls 必须，String,指定的className
	 */
	// 1、是否函数指定的className
	function hasClass(elem, cls) {
	    return elem.className.match(new RegExp('(\\s|^)' + cls + '(\\s|$)'));
	}
	// 2、没有就追加指定className
	function addClass(elem, cls) {
	    if (!hasClass(elem, cls)) {
	        elem.className += " " + cls;
	    }
	}
	// 3、有就移除指定className
	function removeClass(elem, cls) {
	    if (hasClass(elem, cls)) {
	        var reg = new RegExp('(\\s|^)' + cls + '(\\s|$)');
	        elem.className = elem.className.replace(reg, "");
	    }
	}

```

> 四、DOM操作

```javascript

	// 4.1封装指定符号获取DOM
	window.$ = HTMLElement.prototype.$ = function(selector) {
	    return (this == window ? document : this).querySelectorAll(selector);
	}
	// 4.2根据ID获取指定DOM
	function getEleId(id) {
	    return document.getElementById(id);
	}
	// 4.3 向指定DOM追加HTML,id,html都是必须
	function pushHtml(id, html) {
	    return document.getElementById(id).innerHTML = html;
	}


```

> 五、封装綁定事件

```javascript

	/**
	 * @param {*} type 必须，绑定事件类型
	 * @param {*} selector 必须，nodeName / className / id
	 * @param {*} callback 必须，绑定成功后的回调，继续操作DOM
	 */
	function on(type, selector, callback) {
	    document.addEventListener(type, function(e) {
	        e.preventDefault();
	        e.stopPropagation();
	        if (selector == e.target.tagName.toLowerCase() || selector == e.target.className || selector == e.target.id) {
	            callback(e);
	        }
	    })
	}
	// Example
	on("click", "btn", function() {
	    console.log("给btn按钮绑定了点击事件")
	})


```

> 六 、原生JS获取之前(prevAll)或者之后(nextAll)的所有兄弟元素

```javascript

	/**
	 * 
	 * 	类似JQ的 prevAll和nextAll
	 */
	HTMLElement.prototype.prevAll = function() {
	    var parent = this.parentElement;
	    var children = parent.children;
	    var arr = [];
	    for (var i = 0; i < children.length; i++) {
	        var previous = children[i];
	        if (previous == this) {
	            break;
	        }
	        arr.push(previous);
	    }
	    return arr;
	}
	
	HTMLElement.prototype.nextAll = function() {
	    var parent = this.parentElement;
	    var children = parent.children;
	    var arr = [];
	    for (var i = children.length - 1; i >= 0; i--) {
	        var nexts = children[i];
	        if (nexts == this) {
	            break;
	        }
	        arr.unshift(nexts);
	    }
	    return arr;
	}
	// Example
	var temp = function(dom) {
	    console.log("prevAll=", dom.prevAll());
	    console.log("nextAll=", dom.nextAll());
	}

```

> 七、获取URL中指定某个参数的值

```javascript

	/**
	 * 
	 * @param {*} strParame 必须,string，返回指定的值
	 * 
	 */
	function getUrlParameter(strParame) {
	    var args = new Object();
	    var query = location.search.substring(1);
	    var pairs = query.split("&");
	    for (var i = 0; i < pairs.length; i++) {
	        var pos = pairs[i].indexOf('=');
	        if (pos == -1) continue;
	        var argname = pairs[i].substring(0, pos);
	        var value = pairs[i].substring(pos + 1);
	        value = decodeURIComponent(value);
	        args[argname] = value;
	    }
	    return args[strParame];
	}
	// Example	"dsaldlas.csadja.com/fhlfh.html?key=10&id=hello";
	var key = getUrlParameter("key"); //10
	var id = getUrlParameter("hello"); //10

```

> 九、简单的加密和解密

```javascript

	/**
	 * 
	 * @param {*} str 必须，string,要加密的对象 
	 * @param {*} xor 必须，加密解密的计算参数
	 * @param {*} hex 必须，加密解密的计算参数
	 */
	// 加密
	function encrypto(str, xor, hex) {
	    if (typeof str !== 'string' || typeof xor !== 'number' || typeof hex !== 'number') {
	        return;
	    }
	    let resultList = [];
	    hex = hex <= 25 ? hex : hex % 25;
	    for (let i = 0; i < str.length; i++) {
	        // 提取字符串每个字符的ascll码
	        let charCode = str.charCodeAt(i);
	        // 进行异或加密
	        charCode = (charCode * 1) ^ xor;
	        // 异或加密后的字符转成 hex 位数的字符串
	        charCode = charCode.toString(hex);
	        resultList.push(charCode);
	    }
	    let splitStr = String.fromCharCode(hex + 97);
	    let resultStr = resultList.join(splitStr);
	    return resultStr;
	}
	//解密
	function decrypto(str, xor, hex) {
	    if (typeof str !== 'string' || typeof xor !== 'number' || typeof hex !== 'number') {
	        return;
	    }
	    let strCharList = [];
	    let resultList = [];
	    hex = hex <= 25 ? hex : hex % 25;
	    // 解析出分割字符
	    let splitStr = String.fromCharCode(hex + 97);
	    // 分割出加密字符串的加密后的每个字符
	    strCharList = str.split(splitStr);
	    for (let i = 0; i < strCharList.length; i++) {
	        // 将加密后的每个字符转成加密后的ascll码
	        let charCode = parseInt(strCharList[i], hex);
	        // 异或解密出原字符的ascll码
	        charCode = (charCode * 1) ^ xor;
	        let strChar = String.fromCharCode(charCode);
	        resultList.push(strChar);
	    }
	    let resultStr = resultList.join('');
	    return resultStr;
	}
	// Example
	var pwd = "BLUE123456!";
	var enPwd = encrypto(pwd, 135, 25);
	var dePwd = decrypto(enPwd, 135, 25);
	console.log(enPwd); //27z25z1lz2cz2oz2nz2mz34z33z32z3f
	console.log(dePwd); //BLUE123456!

```

> 十、判断两个数组是否相等

```javascript

	/**
	 * @param {*} arr1 必须，Array 
	 * @param {*} arr2 必须，Array
	 * 返回：true/false
	 */
	function arrayEqual(arr1, arr2) {
	    if (arr1 === arr2) return true;
	    if (arr1.length != arr2.length) return false;
	    for (var i = 0; i < arr1.length; ++i) {
	        if (arr1[i] !== arr2[i]) return false;
	    }
	    return true;
	}

```