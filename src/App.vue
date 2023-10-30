<template>
  <div id="app">
    <StyleEditor ref="styleEditor" :code="currentStyle"></StyleEditor>
    <ResumeEditor ref="resumeEditor" :markdown="currentMarkdown" :enableHtml="enableHtml"></ResumeEditor>
  </div>
</template>

<script>
import StyleEditor from './components/StyleEditor'
import ResumeEditor from './components/ResumeEditor'
import './assets/reset.css'

export default {
  name: 'app',
  components: {
    StyleEditor,
    ResumeEditor
  },
  data() {
    return {
      interval: 40,
      currentStyle: '',
      enableHtml: false,
      fullStyle: [
        `/*
*《空理彷徨切图仔》
* 我叫羊醋，是一个前端工程师。
* 我已经切图两年了，刀法精湛。
*/

/* 我更多时候，在写代码，如同变魔术一般*/
* {
  transition: all .3s;
}
/* trance on！磷叶石沉浸式交互界面连接  */
html {
  color: rgb(222,222,222); background: rgb(0,43,54);
}
/* 神经同调装置在基准范围内 */
.styleEditor {
  padding: .5em;
  border: 1px solid;
  margin: .5em;
  overflow: auto;
  width: 45vw; height: 90vh;
}
/* 梦幻光束传导中...*/
.token.selector{ color: rgb(133,153,0); }
.token.property{ color: rgb(187,137,0); }
.token.punctuation{ color: yellow; }
.token.function{ color: rgb(42,161,152); }

/* 3D模拟引擎启用 */
html{
  perspective: 1000px;
}
.styleEditor {
  position: fixed; left: 0; top: 0;
  -webkit-transition: none;
  transition: none;
  -webkit-transform: rotateY(10deg) translateZ(-100px) ;
          transform: rotateY(10deg) translateZ(-100px) ;
}

/* 接下来我给自己准备一个编辑器 */
.resumeEditor{
  position: fixed; right: 0; top: 0;
  padding: .5em;  margin: .5em;
  width: 48vw; height: 90vh;
  border: 1px solid;
  background: white; color: #222;
  overflow: auto;
}
/* 第一锁定器解除，思维连接正常，同步率为1000.000%，移往通道口 */


`,
        `
/* 这个故事好像差点什么
 * 不就像人生一样缺少重点吗，重点什么的，又有意义吗？
 * 无限重复切图的人生
 */
`
        ,
        `
/* 找到重点，只需要一个时刻。一个拥有目标的稍纵即逝的瞬间 */
.resumeEditor{
  padding: 2em;
}
.resumeEditor h2{
  display: inline-block;
  border-bottom: 1px solid;
  margin: 1em 0 .5em;
}
.resumeEditor ul,.resumeEditor ol{
  list-style: none;
}
.resumeEditor ul> li::before{
  content: '•';
  margin-right: .5em;
}
.resumeEditor ol {
  counter-reset: section;
}
.resumeEditor ol li::before {
  counter-increment: section;
  content: counters(section, ".") " ";
  margin-right: .5em;
}
.resumeEditor blockquote {
  margin: 1em;
  padding: .5em;
  background: #ddd;
}
`],
      currentMarkdown: '',
      fullMarkdown: `莎士比亚的十四行诗
----

我的代码是十四行，时空错乱的黑马 身披铠甲的勇士，风卷着我的披风。

少女迷失在古堡
----

* 一步两蹦的跑
* 乌黑的长发
* 森林的密道
* 恶龙的咆哮

青涩的爱恋
----

长枪刺向的喉咙 献血渗透的龙鳞
不存在的公主 拥抱少女的骑士

死亡与梦之约
----

* 公主死了
* 屠龙的勇士还在燃烧

> 这么好的剧本，我不能演砸了。等等，写代码的时候我为什么打这么多中二的汉字啊喂！

`
    }
  },
  created() {
    this.makeResume()
  },

  methods: {
    makeResume: async function () {
      await this.progressivelyShowStyle(0)
      await this.progressivelyShowResume()
      await this.progressivelyShowStyle(1)
      await this.showHtml()
      await this.progressivelyShowStyle(2)
    },
    showHtml: function () {
      return new Promise((resolve, reject) => {
        this.enableHtml = true
        resolve()
      })
    },
    progressivelyShowStyle(n) {
      return new Promise((resolve, reject) => {
        let interval = this.interval
        let showStyle = (async function () {
          let style = this.fullStyle[n]
          if (!style) { return }
          // 计算前 n 个 style 的字符总数
          let length = this.fullStyle.filter((_, index) => index <= n).map((item) => item.length).reduce((p, c) => p + c, 0)
          let prefixLength = length - style.length
          if (this.currentStyle.length < length) {
            let l = this.currentStyle.length - prefixLength
            let char = style.substring(l, l + 1) || ' '
            this.currentStyle += char
            if (style.substring(l - 1, l) === '\n' && this.$refs.styleEditor) {
              this.$nextTick(() => {
                this.$refs.styleEditor.goBottom()
              })
            }
            setTimeout(showStyle, interval)
          } else {
            resolve()
          }
        }).bind(this)
        showStyle()
      })
    },
    progressivelyShowResume() {
      return new Promise((resolve, reject) => {
        let length = this.fullMarkdown.length
        let interval = this.interval
        let showResume = () => {
          if (this.currentMarkdown.length < length) {
            this.currentMarkdown = this.fullMarkdown.substring(0, this.currentMarkdown.length + 1)
            let lastChar = this.currentMarkdown[this.currentMarkdown.length - 1]
            let prevChar = this.currentMarkdown[this.currentMarkdown.length - 2]
            if (prevChar === '\n' && this.$refs.resumeEditor) {
              this.$nextTick(() => this.$refs.resumeEditor.goBottom())
            }
            setTimeout(showResume, interval)
          } else {
            resolve()
          }
        }
        showResume()
      })
    }
  }
}

</script>

<style scoped>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

html {
  min-height: 100vh;
}

* {
  box-sizing: border-box;
}
</style>