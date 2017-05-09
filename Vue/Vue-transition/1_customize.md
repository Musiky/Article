## 使用自定义动画

``` html
<transition name="fade-slide">
    <div v-if="is_show">content</div>
</transition>

<button @click="is_show = !is_show">click me</button>
```

``` css
.fade-slide-enter-active {
    animation: fade-slide-in .5s
}
.fade-slide-leave-active {
    animation: fade-slide-out .5s
}
@keyframes fade-slide-in {
    from {
        opacity: 0;
        transform: translate3d(10px, 0, 0)
    }
    to {
        opacity: 1;
        transform: translate3d(0, 0, 0)
    }
}
@keyframes fade-slide-out {
    from {
        opacity: 1;
        transform: translate3d(0, 0, 0);
    }
    to {
        opacity: 0;
        transform: translate3d(-10px, 0, 0);
    }
}
```
