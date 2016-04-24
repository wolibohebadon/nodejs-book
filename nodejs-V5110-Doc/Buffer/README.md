#Buffer

##穩定性：2-穩定


優先介紹在2015 ECMAScript的TypedArray,JavaScript的語言沒有閱讀或者操作的二進制數據匯集的機制。在TCP檔案系統的操作下，Buffer類是被介紹為Node.jsAPI的一部份讓他可以跟其他八進制字流的東西互動。

現今，TypedArray已經被加進ES6底下，在使用Node.js的case下，
Buffer類實施Unit8Array是一個更適合更優化的方式。

舉例而言，Buffer類是一個更像一個整數的陣列，但是他是對應的固定大小，原始內存分配在V8堆疊的外面，在Buffer被創造而且不能重新調整的狀態下，Buffer的size會被建立。

在Node.js裡面，Buffer class 是一個全域範圍，讓他不會是一個永遠會被使用的require('buffer')的這個方法。

```javascript

const buf1 = Buffer.alloc(10);
  //建立一個長度為10的Buffer

const buf2 = Buffer.alloc(10, 1);
  //建立一個長度為10的Buffer,並且設置在0x01

const buf3 = Buffer.allocUnsafe(10);
  //建立一個未初始化並且長度為10的Buffer
  //呼叫Buffer.alloc()更快但會回傳
  //Buffer實例可以包含一個被需要舊資料
  //覆蓋必須使用fill()或者write()

const buf4 = Buffer.from([1,2,3]);
  //建立一個Buffer包含[1,2,3].
74
const buf5 = Buffer.from('test');
  //建立一個Buffer包含ASCII位元[74,65,73,].

const buf6 = Buffer.from('tést', 'utf8');
  //建立一個Buffer包含UTF8位元[74,c3,a9,73]
  
```

##Buffer.from(), Buffer.alloc(), and Buffer.allocUnsafe()

從歷史上來看，Buffer的例子已經被創造，使用在Buffer的建構函數上，而Buffer建構函數是在提供下面這個參數下，分配不同的回傳Buffer：

- 在Buffer()傳遞一個數字當作第一個參數(e.g new Buffer(10)),分配一個新的指定size的Buffer物件。記憶體分配給這樣的Buffer例子不是初始化的，Buffer例子也可以包含靈敏的資料。例如Buffer物件必須是通過手動的方式使用buf.fill(0)或者完全寫入Buffer進行初始化。雖然這種行為是故意去提高性能但是開發經驗已經證明需要更加明確的對創造一個更慢卻很安全的Buffer與創建一個快速但未初始化的Buffer之間的區分。
- 傳遞一個字串，陣列，或者一個Buffer 把做為第一個參數的副本傳遞到物件資料的Buffer內。
- 傳遞一個ArrayBuffer並且回傳一個Buffer給ArrayBuffer去分配共享的記憶體。

##Class:Buffer

###buf[index]

獲取或者設置在指定[index]索引位置的8位元位元組。這個值是指單個位元組，所以這個值必須在合法的範圍，16進制的0x00`到0xFF，或者0 到255。
例如：複製ASCII到buffer，一次一個位元組：

```javascript

const str = "Node.js";
const buf = Buffer.allocUnsafe(str.length);

for (var i = 0; i < str.length ; i++) {
  buf[i] = str.charCodeAt(i);
}

console.log(buf.toString('ascii'));
  //印出: Node.js
  
```

###buf.compare(target[, targetStart[, targetEnd[, sourceStart[, sourceEnd]]]])

- target [Buffer]
- targetStart [Integer] 在開始比較當下，target中的偏移量。默認值為0。
- targetEnd [Integer] 在結束比較當下，target的偏移量。當targetStar為undefined時，忽略它。默認值為target.byteLength。
- sourceStart [Integer] 在開始比較當下，buf的偏移量。當targetStart 為undefined時，請忽略。默認值= 0。
- sourceEnd [Integer] 在結束比較當下，buf的偏移量。當targetStar 為undefined時，請忽略。默認值=buf.byteLength。
- Return: [Number]

比較兩個buffer的例子，並回傳數字說明buf是在之前、之後，還是跟target同個排序順序。比較是基於在每個buffer裡的位元組實際序列。

- 如果target跟buf一樣，0會被返回
- 如果target在排序時，需要再buf之前，1會被返回。
- 如果target在排序時，需要再buf之後，-1會被返回。

```javascript

const buf1 = Buffer.from('ABC');
const buf2 = Buffer.from('BCD');
const buf3 = Buffer.from('ABCD');

console.log(buf1.compare(buf1));
  // 印出: 0
console.log(buf1.compare(buf2));
  // 印出: -1
console.log(buf1.compare(buf3));
  // 印出: 1
console.log(buf2.compare(buf1));
  // 印出: 1
console.log(buf2.compare(buf3));
  // 印出: 1

[buf1, buf2, buf3].sort(Buffer.compare);
  // 排序 [buf1, buf3, buf2]

```
可選擇的targetStart，targetEnd，sourceStart和sourceEnd參數可以在兩個buffer對象的特定範圍內被用來限制比較。

```javascript

const buf1 = Buffer.from([1, 2, 3, 4, 5, 6, 7, 8, 9]);
const buf2 = Buffer.from([5, 6, 7, 8, 9, 1, 2, 3, 4]);

console.log(buf1.compare(buf2, 5, 9, 0, 4));
  // 印出: 0
console.log(buf1.compare(buf2, 0, 6, 4));
  // 印出: -1
console.log(buf1.compare(buf2, 5, 6, 5));
  // 印出: 1

```
如果: targetStart < 0, sourceStart < 0, targetEnd > target.byteLength orsourceEnd > source.byteLength，RangeError會被拋出。

###buf.copy(targetBuffer[, targetStart[, sourceStart[, sourceEnd]]])

- targetBuffer [Buffer] Buffer 複製進去
- targetStart [Number] Default: 0
- sourceStart [Number] Default: 0
- sourceEnd [Number] Default: buffer.length
- Return: [Number] 複製字符串的位元組

從Buffer的一個區域中複製資料到targetBuffer的區域，即使目標儲存區與來源重疊。
舉例：建立兩個Buffer，然後將buf1中第16位元組到第19位元組間的資訊複製到buf2中，並使在buf2中新的字串首字元位於第8位元組：

```javascript

const buf1 = Buffer.allocUnsafe(26);
const buf2 = Buffer.allocUnsafe(26).fill('!');

for (var i = 0 ; i < 26 ; i++) {
  buf1[i] = i + 97; // 97 is ASCII a
}

buf1.copy(buf2, 8, 16, 20);
console.log(buf2.toString('ascii', 0, 25));
  // 印出: !!!!!!!!qrst!!!!!!!!!!!!!

```

構建一個Buffer，然後從一個相同的Buffer中，從一個區域中複製資料到重疊區域。

```javascript

const buf = Buffer.allocUnsafe(26);

for (var i = 0 ; i < 26 ; i++) {
  buf[i] = i + 97; // 97 is ASCII a
}

buf.copy(buf, 0, 4, 10);
console.log(buf.toString());

// efghijghijklmnopqrstuvwxyz

```

###buf.entries()
- otherBuffer [Buffer]

從Buffer的內容裡，創建並返回 iterator [index, byte]。

```javascript

const buf = Buffer.from('buffer');
for (var pair of buf.entries()) {
  console.log(pair);
}
// 印出:
//   [0, 98]
//   [1, 117]
//   [2, 102]
//   [3, 102]
//   [4, 101]
//   [5, 114]

```

###buf.equals(otherBuffer)

- otherBuffer [Buffer]
- Return: [Boolean]
返回一個boolean，說明this跟otherBuffer是否擁有相同的位元組。

```javascript

const buf1 = Buffer.from('ABC');
const buf2 = Buffer.from('414243', 'hex');
const buf3 = Buffer.from('ABCD');

console.log(buf1.equals(buf2));
  // 印出: true
console.log(buf1.equals(buf3));
  // 印出: false
  
```

###buf.fill(value[, offset[, end]][, encoding])


- value  [String] | [Buffer] | [Number]
- offset  [Number] Default: 0
- end  [Number] Default: buf.length
- encoding  [String] Default: 'utf8'
- Return: [Buffer]

用指定的質填在Buffer裡面。如果offset(默認值為0)且end(默認值為buf.length) 沒有明確給出，就會填充整個buffer。
這個方法從Buffer裡面返回一個參考值，所以呼叫可以被鏈接。這意味著一個小的、簡化型的是可以用來創造Buffer。在一行裡面，是允許創造、並用用Buffer來填滿完成。

const b = Buffer.alloc(50, 'h');
console.log(b.toString());
   // 印出: hhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhh

Fill()執行後會直接寫位元組到Buffer裡面。無論位元組被寫進適合的buffer，最終的寫入落到多位元的字符集。

```javascript

Buffer.alloc(3, '\u0222');
  // 印出: <Buffer c8 a2 c8>

```

###buf.indexOf(value[, byteOffset][, encoding])

- value [String] | [Buffer] | [Number]
- byteOffset [Number] Default: 0
- encoding [String] Default: 'utf8'
- Return: [Number]

當它返回Buffer的value的起始索引區或者-1時，如果Buffer並沒有包含value，操作類似Array#indexOf()。Value可以是一個字串、一個Buffer、或者一個數字。字符串默認情況下解釋為UTF-8。Buffer將會用到整個Buffer(來跟一部份Buffer使用buf.slice()做比較)。數字可以被訂在0到255之間。
const buf = Buffer.from('this is a buffer');

```javascript

buf.indexOf('this');
  // 回傳 0
buf.indexOf('is');
  // 回傳 2
buf.indexOf(Buffer.from('a buffer'));
  // 回傳 8
buf.indexOf(97); // ascii for 'a'
  // 回傳 8
buf.indexOf(Buffer.from('a buffer example'));
  // 回傳 -1
buf.indexOf(Buffer.from('a buffer example').slice(0,8));
  // 回傳 8

const utf16Buffer = Buffer.from('\u039a\u0391\u03a3\u03a3\u0395', 'ucs2');

utf16Buffer.indexOf('\u03a3',  0, 'ucs2');
  // 回傳 4
utf16Buffer.indexOf('\u03a3', -4, 'ucs2');
  // 回傳 6

```

###buf.includes(value[, byteOffset][, encoding])

- value [String] | [Buffer] | [Number]
- byteOffset [Number] Default: 0
- encoding [String] Default: 'utf8'
- Return: [Boolean]


操作類似Array#includes().value可以是一個字串、一個Buffer或一個數字。字串會被解釋為UTF8，除非他被以encoding參數覆蓋。Buffer會用到整個Buffer(來跟一部份Buffer使用buf.slice()做比較)。數字可以被訂在0到255之間。
byteOffset包含了搜尋起始裡 buf的索引。

```javascript

const buf = Buffer.from('this is a buffer');

buf.includes('this');
  // 回傳 true
buf.includes('is');
  // 回傳 true
buf.includes(Buffer.from('a buffer'));
  // 回傳 true
buf.includes(97); // ascii for 'a'
  // 回傳 true
buf.includes(Buffer.from('a buffer example'));
  // 回傳 false
buf.includes(Buffer.from('a buffer example').slice(0,8));
  // 回傳 true
buf.includes('this', 4);
  // 回傳 false

```













