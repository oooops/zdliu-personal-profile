# js模拟promise

```js

function Promise() {
 this.callbacks=[] 
}

Promise.prototype={
  constructor:Promise,
  resolve:function (result) {
  },
  reject:function(result){
  },
  complete:function(type,result) {
    while (this.callbacks[0]){
      this.callbacks.unshift()[type](result)
    }
  },
  then:function(resolve,reject) {
    this.callbacks.push({resolve,reject})
    return this
  }
}

```