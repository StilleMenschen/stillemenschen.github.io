# 多线程

```javascript
const { Worker, isMainThread, workerData, parentPort } = require("worker_threads");
const numCPUs = require("os").cpus().length;

/**比较数组两个位置的值大小 */
function compare(arr, i, j) {
  if (arr[i] < arr[j]) return -1;
  else if (arr[i] > arr[j]) return 1;
  else return 0;
}

/**交换数组两个位置的值 */
function swap(arr, i, j) {
  [arr[i], arr[j]] = [arr[j], arr[i]];
  return arr;
}

/**生成一个指定范围的随机数 */
function getRandomNumber(min, max) {
  let range = max - min;
  let rand = Math.random();
  return min + Math.round(rand * range);
}

function quicksort(array) {
  let length = array.length;
  let stack = [
    {
      first: 0,
      last: length,
    },
  ];
  while (stack.length) {
    let { first, last } = stack.pop();
    if (last - first < 5) {
      for (let i = first + 1; i < last; i++) {
        let j = i - 1;
        while (j <= first) {
          if (compare(array, j, j + 1) <= 0) {
            break;
          }
          array = swap(array, j, j + 1);
          j--;
        }
      }
      continue;
    }
    let j = first;
    let i = Math.floor((first + last) / 2);
    let k = last - 1;
    if (compare(array, k, i) < 0) {
      array = swap(array, k, i);
    }
    if (compare(array, k, j) < 0) {
      array = swap(array, k, j);
    }
    if (compare(array, j, i) < 0) {
      array = swap(array, j, i);
    }
    let pivot = j;
    let left = first;
    let right = last;
    while (true) {
      right--;
      while (right > first && compare(array, right, pivot) >= 0) {
        right--;
      }
      left++;
      while (left < last && compare(array, left, pivot) <= 0) {
        left++;
      }
      if (left > right) {
        break;
      }
      array = swap(array, left, right);
    }
    array = swap(array, pivot, right);
    let n1 = right - first;
    let n2 = last - left;
    if (n1 > 1) {
      stack.unshift({
        first: first,
        last: right,
      });
    }
    if (n2 > 1) {
      stack.unshift({
        first: left,
        last: last,
      });
    }
  }
  return array;
}

if (isMainThread) {
  /*主线程*/
  function runService(workerData) {
    return new Promise((resolve, reject) => {
      const task = new Worker(__filename, { workerData });
      task.on("message", (msg) => {
        console.log(`[Task ${msg}] Receive message.`);
        resolve(msg);
      });
      task.on("error", reject);
      task.on("exit", (code) => {
        if (code !== 0) reject(new Error(`Worker stopped with exit code ${code}.`));
      });
    });
  }

  async function run(index) {
    const result = await runService(index);
    console.log(`[Main ${process.pid}] Task ${result} has ended.`);
  }

  console.log(`[Main ${process.pid}] started`);
  for (let index = 0; index < numCPUs; index++) {
    run(index);
  }
} else {
  /*子线程*/
  console.log(`[Task ${workerData}] started`);
  let sourceList = [];
  for (let i = 0; i < 10; i++) {
    sourceList.push(getRandomNumber(1, 100));
  }
  console.log(`[Task ${workerData}] Before ${sourceList.join(", ")}`);
  sourceList = quicksort(sourceList);
  console.log(`[Task ${workerData}] After ${sourceList.join(", ")}`);
  parentPort.postMessage(workerData);
}
```

Last Modified 2021-05-01
