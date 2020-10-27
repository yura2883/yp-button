<template>
    <div v-bind:class="{'yp-button': true, 'disabled': !!disabled}">
        <button 
            class="yp-button_button" 
            v-on:mousedown="pointerdown" v-on:pointerdown="pointerdown" v-on:touchstart="pointerdown"
            v-on:mouseup="pointerup" v-on:pointerup="pointerup" v-on:touchend="pointerup"
            v-on:touchmove="touchmove"
            v-on:mouseout="pointerout"
            v-on:click="clicked">
            <slot></slot>
        </button>
    </div>
</template>

<script>
import {Tween, Easing} from 'es6-tween'

const config = {
    startRadius: 10,
    opacity: 0.3,
    timeRipple: 400,
    timeFadeOut: 300,
    smooth: 3
}

function findParentMatch(el, sel) {
    var curEl = el
    while(curEl && !curEl.parentNode.querySelector(sel)) curEl=curEl.parentNode
    return curEl
}

function animate(tw) {
    if(tw.isPlaying()) {
        tw.update()
        requestAnimationFrame(function() {
            animate(tw)
        })
    }
}

function getGradient(x, y, o, r1, s, inv) {
    var rc=inv ? '0,0,0,' : '255,255,255,'
    return `radial-gradient(circle at ${x}px ${y}px, rgba(${rc}${o}) 0px, rgba(${rc}${o}) ${Math.round(r1-s)}%, rgba(${rc}0) ${Math.round(r1+s)}%, rgba(${rc}0) 120%)`
}

// function toStr(o) {
//     var ret=[], i
//     for(i in o) {
//         ret.push(i)     
//     }
//     return ret.join(', ')
// }


/**
 * события pointer не поддерживаются safari и chrome на Яблоках, а также firefox в Android (там только touch)
 * если с самого начала срабатывает touchstart, то он предпочтительнее всего остального (и он всегда есть на сенсорах)
 * на современном chrome в android срабатывают все 3 события:
 * - pointerdown, 
 * - потом touchstart (чтобы можно было как-то обработать сдвиг пальца)
 * - и наконец mousedown (для совместимости со старыми скриптами при отпускании пальца)
 *
 * на современных компьютерах срабатывают 2 события 
 * - pointerdown (т.е. он универсален, но если его нет, то остаются мышь или touch)
 * - mousedown
 *
 * если после нажатия произошел сдвиг по тачу, то pointerup не срабатывает, даже если был pointerdown
 *
 * проблема в тачами в точ. что вместо event нужо обрабатывать event.touches:array
 * в каждом элементе которого нет offsetX, offsetY
 */

export default {
    props: ['inverted', 'disabled'],
    data: function() {
        return {
            animState: {
                r: config.startRadius,
                o: config.opacity
            },
            easing: Easing.Quadratic.InOut,
            pointer: {
                x: 0,
                y: 0
            },
            btn: null,
            tweens: {
                ripple: null,
                fadeOut: null
            },
            nomouse: 0,
            notouch: 0,
            notNeedTouchEnd: 0
        }
    },
    methods: {
        touchstart: function(ev) {
            this.$emit('touchstart', ev.type)
        },
        touchmove: function(ev) {
            this.$emit('touchmove', ev.type)
        },
        touchend: function(ev) {
            this.$emit('touchend', ev.type)
        },
        pointerout: function(ev) {
            ev.buttons>0 && this.pointerup(ev)
        },
        pointerdown: function(ev) {
            this.$emit(ev.type, ev.type)
            this.notNeedTouchEnd = 0
            if(this.nomouse && ev.type == 'mousedown') {ev.stopPropagation(); return}
            if(this.notouch && ev.type == 'touchstart') {ev.stopPropagation(); return}

            var comp=this, el=findParentMatch(ev.target, '.yp-button_button'), x, y, animState=comp.animState, t
            if(ev.type == 'pointerdown') {comp.nomouse=1; comp.notouch=1}
            ev.stopPropagation()
            if(comp.disabled) return
            animState.r = config.startRadius
            animState.o = config.opacity
            if((t=comp.tweens.ripple) && t.isPlaying()) t.stop()
            if((t=comp.tweens.fadeOut) && t.isPlaying()) t.stop()
            comp.btn = el
            comp.pointer.x = x = ev.offsetX
            comp.pointer.y = y =ev.offsetY
            el.style.backgroundImage=getGradient(x, y, animState.o, config.startRadius, config.smooth, comp.inverted)
            let tw = new Tween(animState)
                .to({r: 100}, config.timeRipple)
                .easing(comp.easing)
                .on('update', ({r}) => {
                    el.style.backgroundImage=getGradient(x, y, animState.o, r, config.smooth, comp.inverted)
                })
                .on('complete', () => {
                    let tw2=comp.tweens.fadeOut
                    if(tw2 && !tw2.isPlaying()) {
                        animState.o=config.opacity
                    }
                })
                .start()

            animate(tw)
            comp.tweens.ripple = tw
        },
        pointerup: function(ev) {
            this.$emit(ev.type, ev.type)
            if(ev.type=='mouseup' && this.nomouse) {ev.stopPropagation(); return}
            if(ev.type=='touchend' && this.notNeedTouchEnd) {ev.stopPropagation(); return}
            
            // если pointerup сработал, то touchend уже не требуется
            if(ev.type=='pointerup') this.notNeedTouchEnd=1

            var comp=this, animState=comp.animState, x=comp.pointer.x, y=comp.pointer.y, el=comp.btn, to={o: 0}
            if(!el) return
            ev.stopPropagation()
            if(comp.disabled) return
            let tw = new Tween(animState)
                .to(to, config.timeFadeOut)
                .easing(comp.easing)
                .on('update', () => {
                    el.style.backgroundImage=getGradient(x, y, animState.o, animState.r, config.smooth, comp.inverted)
                })
                .on('complete', () => {
                    let tw1=comp.tweens.ripple
                    if(!tw1.isPlaying()) animState.o=config.opacity
                    animState.r=config.startRadius
                })
                .start()
            animate(tw)
            comp.tweens.fadeOut = tw
        },
        clicked: function(ev) {
            if(!this.disabled) this.$emit('click', ev)
        }
    }
}
</script>

<style lang="less">
@colorControl: #2680D4;
@colorAhtung: #DB2C5D;
@colorControlText: #fff;

.yp-button {
    display: inline-block;
    margin: 5px 5px 0 0;
    .yp-button_button {
        border: none;
        background-color: @colorControl;
        color: @colorControlText;
        font-size:16px;
        padding:5px 10px;
        border-radius: 5px;
        &, & * {
            -webkit-touch-callout: none;
            -moz-user-select: none;
            user-select: none;
        }
        &:active, &:focus {
            outline: none;
        }
        &::-moz-focus-inner {
            border: 0;
        }
    }
    &.disabled {
        >.yp-button_button {
            background-color: lighten(@colorControl, 20%);
            color: darken(@colorControlText, 10%);
        }
    }
    &.ahtung {
        >.yp-button_button {
            background-color: @colorAhtung;
        }
        &.disabled {
            >.yp-button_button {
                background-color: lighten(@colorAhtung, 20%);
            }
        }
    }
    &.white { 
        /* нужна ли кнопка, может класс inverted */
        >.yp-button_button {
            background-color: #fff;
            color: #222;
        }
    }
    &.link {
        >.yp-button_button {
            /* фактически тоже инверсивный класс, но только с прозрачным фоном */
            background-color: transparent;
            color: @colorControl;
            a {
                color: @colorControl;
                text-decoration: none;
            }
        }
        &.disabled {
            >.yp-button_button {
                color: #777;
            }
        }

    }
}
</style>
