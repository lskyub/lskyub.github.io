---
layout: post
title: "Livedata Debounce 처리"
date: 2024-05-22 15:00:00 +0900
categories: abdriud.
tags: []
---

flow를 사용하지 않고 livedata를 Observer로 사용할때 

room db를 observer 할 경우 데이터가 연속적으로 들어갔을때 계속 호출이 발생 하는 이슈가 있어 해당 현상을 처리할 필요성이 발생

Lucio Maciel github에서 해당 코드를 찾아 사용

## 사용방법
```kotlin
    private var _values = MutableLiveData<String>()
    val values: LiveData<String> = _values

    values.debounce(1000, TimeUnit.MILLISECONDS).observe(activity) {
        //1초 후 이벤트 발생
    }
```

## 전체코드
```kotlin
class DebounceLiveData<Source>(
    private val source: LiveData<Source>,
    private val debounceMs: Long
) : LiveData<Source>(), CoroutineScope {
    private val job = SupervisorJob()
    override val coroutineContext: CoroutineContext
        get() = Dispatchers.Main + job
    private var debounceJob: Job? = null

    private val observer = Observer<Source> { source ->
        debounceJob?.cancel()
        debounceJob = launch {
            delay(debounceMs)
            value = source
        }
    }

    override fun onActive() {
        source.observeForever(observer)
    }

    override fun onInactive() {
        debounceJob?.cancel()
        source.removeObserver(observer)
    }
}

fun <Source> LiveData<Source>.debounce(
    time: Long,
    unit: TimeUnit = TimeUnit.MILLISECONDS
): LiveData<Source> {
    val timeMs = when (unit) {
        TimeUnit.NANOSECONDS -> unit.toMillis(time)
        TimeUnit.MICROSECONDS -> unit.toMillis(time)
        TimeUnit.MILLISECONDS -> time
        TimeUnit.SECONDS -> unit.toMillis(time)
        TimeUnit.MINUTES -> unit.toMillis(time)
        TimeUnit.HOURS -> unit.toMillis(time)
        TimeUnit.DAYS -> unit.toMillis(time)
    }

    return DebounceLiveData(this, timeMs)
}
```
### 참고
[luciofm github](https://gist.github.com/luciofm/3ae1c0869cf9a05cd9a2e9e5baa9c1c9)
