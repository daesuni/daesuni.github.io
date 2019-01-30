---
layout: post
title: Javascript/Jquery array distinct
category: Javascript/Jquery
---

배열에 중복요소가 있는지 체크

```javascript
Array.prototype.distinct = function() {
    var a = this.concat();
    for(var i=0; i<a.length; ++i) {
        for(var j=i+1; j<a.length; ++j) {
            if(a[i] === a[j])
                a.splice(j--, 1);
        }
    }
    return a;
};
```
