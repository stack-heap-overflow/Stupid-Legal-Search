<template>
    <div class="page">
        <div class="title">
            <div class="realtitle" v-html="detail.标题"></div>
            <div>
                <span v-html="detail.经办法院"></span>
                <span>  |  </span>
                <span v-html="detail.案号"></span>
                <span>  |  </span>
                <span v-html="detail.裁判时间"></span>
            </div>
        </div>
        <el-divider></el-divider>
        <div class="content">
            <template v-for="key in (detail.案例类别=='普通案例' ? showkeys : showkeys_auth)">
                <div v-bind:key="key" v-if="key in detail && detail[key]">
                    <div class="subtitle" v-html="key"></div>
                    <div class="detail" v-html="detail[key]"></div>
                </div>
            </template>
            <el-divider></el-divider>
            <p></p>
            <div v-if="detail.案例类别=='普通案例'">
              <template v-for="(key, i) in recomkeys">
                <div v-bind:key="i" style="padding-bottom:20px">
                  <div class="subtitle2" v-html="key"></div>
                  <template v-for="(name, j) in detail[recomkeys_ext[i]]">
                    <div v-bind:key="j">
                      <div class="detail-bold" v-html="name" v-if="detail[recomkeys[i]][j].length > 0"></div>
                      <div>
                        <template v-for="(item, k) in detail[recomkeys[i]][j]">
                          <div v-bind:key="k">
                            <a v-bind:href="jump_url(item.pid)" target="_blank">{{ item.WS }}</a>
                          </div>
                        </template>
                      </div>
                    </div>
                  </template>
                </div>
              </template>
            </div>
            <div>
              <div class="subtitle3">类似案件推荐(测试功能)</div>
              <div style="padding-top:20px">
                <template v-for="(item, i) in simrecom">
                  <div v-bind:key="i">
                    <a v-bind:href="jump_url(item.index)" target="_blank">{{ item.标题 }}</a>
                  </div>
                </template>
              </div>
            </div>
        </div>
    </div>
</template>

<script>
import { GET } from '../api/index'
import { replaceAll } from '../api/utils'
export default {
  name: 'detail',
  data () {
    return {
      detail: null,
      showkeys: ['当事人', '当事人段', '诉讼记录', '案件基本情况', '案件基本情况段', '案件事实段', '裁判分析过程', '起诉分析段', '判决结果', '本审判决结果', '文尾'],
      showkeys_auth: ['案例原则', '全文'],
      recomkeys: ['同法官案件推荐', '同法律案件推荐'],
      recomkeys_ext: ['法官人员完整', '法律法条分组'],
      searchcut: '',
      simrecom: []
    }
  },
  computed: {
  },
  created () {
    GET('/detail/', this.$route.query).then(result => {
      this.detail = result
      this.searchcut = result.searchcut
      delete this.detail.searchcut
      for (var i in this.showkeys) {
        var key = this.showkeys[i]
        if (key in this.detail) {
          this.detail[key] = this.render(this.detail[key])
        }
      }
      for (var j in this.showkeys_auth) {
        var akey = this.showkeys_auth[j]
        if (akey in this.detail) {
          this.detail[akey] = this.render(this.detail[akey])
        }
      }
    })

    GET('/similar_docs/', {pid: this.$route.query.index}).then(response => {
      this.simrecom = response.result
    })
  },
  methods: {
    render (value) {
      if (value === null) return null
      value = value.trim()
      value = replaceAll(value, ' ', '<br>')
      for (var i in this.searchcut) {
        var word = this.searchcut[i]
        value = replaceAll(value, word, '<span style="color:red">' + word + '</span>')
      }
      return value
    },
    jump_url (index) {
      return 'detail?index=' + String(index)
    }
  }
}
</script>

<style scoped>
.content {
    text-align: left;
    padding: 10px;
}
.page {
  width: 80%;
  margin: 0 auto;
  line-height: 30px;
}
.subtitle {
  border-left: 10px solid #1e90ff;
  padding-left: 20px;
  font-size: 24px;
  font-weight: bold;
}
.subtitle2 {
  border-left: 10px solid #ff4500;
  padding-left: 20px;
  font-size: 24px;
  font-weight: bold;
}
.subtitle3 {
  border-left: 10px solid #7cfc00;
  padding-left: 20px;
  font-size: 24px;
  font-weight: bold;
}
.detail {
  padding-bottom: 20px;
  padding-top: 20px;
}
.detail-bold {
  padding-bottom: 20px;
  padding-top: 20px;
  font-weight: bold;
}
.realtitle {
  font-size: 32px;
  font-weight: bolder;
  padding: 20px;
}
</style>
