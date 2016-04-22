#Buffer

##穩定性：2-穩定


優先介紹在2015 ECMAScript的TypedArray,JavaScript的語言沒有閱讀或者操作的二進制數據匯集的機制。在TCP檔案系統的操作下，Buffer類是被介紹為Node.jsAPI的一部份讓他可以跟其他八進制字流的東西互動。

現今，TypedArray已經被加進ES6底下，在使用Node.js的case下，
Buffer類實施Unit8Array是一個更適合更優化的方式。

舉例而言，Buffer類是一個更像一個整數的陣列，但是他是對應的固定大小，原始內存分配在V8堆疊的外面，在Buffer被創造而且不能重新調整的狀態下，Buffer的size會被建立。

在Node.js裡面，Buffer class 是一個全域範圍，讓他不會是一個永遠會被使用的require('buffer')。

```javascript
const buf1 = Buffer.alloc(10);
// Creates a zero-filled Buffer of length 10.

const buf2 = Buffer.alloc(10, 1);
  // Creates a Buffer of length 10, filled with 0x01.

const buf3 = Buffer.allocUnsafe(10);
  // Creates an uninitialized buffer of length 10.
  // This is faster than calling Buffer.alloc() but the returned
  // Buffer instance might contain old data that needs to be
  // overwritten using either fill() or write().

const buf4 = Buffer.from([1,2,3]);
  // Creates a Buffer containing [01, 02, 03].

const buf5 = Buffer.from('test');
  // Creates a Buffer containing ASCII bytes [74, 65, 73, 74].

const buf6 = Buffer.from('tést', 'utf8');
  // Creates a Buffer containing UTF8 bytes [74, c3, a9, 73, 74].
```

#Buffer.from(), Buffer.alloc(), and Buffer.allocUnsafe()

從歷史上來看，Buffer的例子已經被創造，使用在Buffer的建構函數上，而Buffer建構函數是在提供下面這個參數下，分配不同的回傳Buffer：

- 在Buffer()傳遞一個數字當作第一個參數(e.g new Buffer(10)),分配一個新的指定size的Buffer物件。記憶體分配給這樣的Buffer例子不是初始化的，Buffer例子也可以包含靈敏的資料


Such Buffer objects must be initialized manually by using either buf.fill(0) or by writing to the Buffer completely. While this behavior is intentional to improve performance, development experience has demonstrated that a more explicit distinction is required between creating a fast-but-uninitialized Buffer versus creating a slower-but-safer Buffer.


- Passing a string, array, or Buffer as the first argument copies the passed object's data into the Buffer.

- Passing an ArrayBuffer returns a Buffer that shares allocated memory with the given ArrayBuffer.





















