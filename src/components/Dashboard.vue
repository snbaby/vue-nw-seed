<template>
  <div class="card-list" ref="content">
    <a-list
      :grid="{gutter: 24, lg: 3, md: 2, sm: 1, xs: 1}"
      :dataSource="dataSource"
    >
      <a-list-item slot="renderItem" slot-scope="item">
        <template v-if="item === null">
          <a-button class="new-btn" type="dashed" @click="addUser">
            <a-icon type="plus"/>
            新增用户
          </a-button>
        </template>
        <template v-else>
          <a-card :hoverable="true">
            <a-card-meta>
              <div style="margin-bottom: 3px" slot="title">{{ item.title }}</div>
              <a-avatar class="card-avatar" slot="avatar" :src="item.avatar" size="large"/>
              <div class="meta-content" slot="description">{{ item.content }}</div>
            </a-card-meta>
            <template class="ant-card-actions" slot="actions">
              <a>编辑</a>
              <a>删除</a>
            </template>
          </a-card>
        </template>
      </a-list-item>
    </a-list>
    <a-modal
      title="登录"
      v-model="visible"
      :width="400"
      @ok="handleOk"
      @cancel="handleCancel"
    >
      <div style="width: 100%;height: 100%;text-align: center"><img :src="qrCode"></div>

    </a-modal>
  </div>
</template>

<script>
const request = require('request')

export default {
  name: 'hello',
  data () {
    return {
      dataSource: [],
      visible: false,
      qrCode: '',
      lgToken: ''
    }
  },
  created () {
    this.dataSource.push(null)
  },
  methods: {
    addUser () {
      /* this.dataSource.push({
        title: '榕易的易',
        content: '四川省成都市天府新区'
      })
      console.log(this.dataSource) */
      this.showModal()
    },
    showModal () {
      const self = this
      let count = Math.floor(Math.random() * 900) + 100
      let options = {
        method: 'get',
        url: `https://qrlogin.taobao.com/qrcodelogin/generateQRCode4Login.do?adUrl=&adImage=&adText=&viewFd4PC=&viewFd4Mobile=&from=tb&appkey=00000000&umid_token=&_ksTS=${new Date().getTime()}_${count}&callback=jsonp${count + 1}`,
        headers: {
          'Content-Type': 'application/x-www-form-urlencoded'
        }
      }

      request(options, function (err, res, body) {
        if (err) {
          console.log(err, res, body)
        } else {
          let jsb = JSON.parse(body.replace(`(function(){jsonp${count + 1}(`, '').replace(');})();', ''))
          self.qrCode = `https://${jsb.url}`
          self.lgToken = jsb.lgToken
          console.log(err, res, body, jsb)
          self.visible = true
        }
      })
    },
    handleOk (e) {
      const self = this
      let count = Math.floor(Math.random() * 900) + 100
      let options = {
        method: 'get',
        url: `https://qrlogin.taobao.com/qrcodelogin/qrcodeLoginCheck.do?lgToken=${self.lgToken}&defaulturl=&_ksTS=${new Date().getTime()}_${count}&callback=jsonp${count + 1}`,
        headers: {
          'Content-Type': 'application/x-www-form-urlencoded'
        }
      }

      request(options, function (err, res, body) {
        if (err) {
          console.log(err, res, body)
          self.$notification['error'].open({
            message: '登录失败',
            description: '请使用淘宝客户端扫码后，再进行登录.',
            onClick: () => {
              console.log('Notification Clicked!')
            }
          })
        } else {
          console.log(err, res, body)
          let jsb = JSON.parse(body.replace(`(function(){jsonp${count + 1}(`, '').replace(');})();', ''))
          if (!jsb.url) {
            self.$notification.open({
              message: '登录失败',
              description: '请使用淘宝客户端扫码后，再进行登录.',
              onClick: () => {
                console.log('Notification Clicked!')
              }
            })
            return
          }
          self.login(jsb.url)
          const nick = self.getQueryVariable('uid', jsb.url).replace('cntaobao', '')
          self.dataSource.push({
            title: nick,
            content: '四川省成都市天府新区',
            avatar: `https://wwc.alicdn.com/avatar/getAvatar.do?userNick=${nick}&width=50&height=50&type=sns&_input_charset=UTF-8`
          })
          self.visible = false
        }
      })
    },
    handleCancel (e) {
      this.visible = false
    },
    login (url) {
      /* return
      let options = {
        method: 'get',
        url: url,
        headers: {
          'Content-Type': 'application/x-www-form-urlencoded'
        }
      }

      request(options, function (err, res, body) {
        if (err) {
          console.log(err, res, body)
        } else {
          console.log(err, res, body)
        }
      }) */
    },
    getQueryVariable (variable, url) {
      var query = url.split('?')[1]
      console.log(query)
      var vars = query.split('&')
      console.log(vars)
      for (var i = 0; i < vars.length; i++) {
        var pair = vars[i].split('=')
        console.log(pair)
        if (pair[0] === variable) {
          console.log(pair[1])
          return pair[1]
        }
      }
      console.log(false)
      return (false)
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="less" scoped>
  .card-list {
    height: 100%;
  }
  .card-avatar {
    width: 48px;
    height: 48px;
    border-radius: 48px;
  }

  .ant-card-actions {
    background: #f7f9fa;
    li {
      float: left;
      text-align: center;
      margin: 12px 0;
      color: rgba(0, 0, 0, 0.45);
      width: 50%;

      &:not(:last-child) {
        border-right: 1px solid #e8e8e8;
      }

      a {
        color: rgba(0, 0, 0, .45);
        line-height: 22px;
        display: inline-block;
        width: 100%;
        &:hover {
          color: #1890ff;
        }
      }
    }
  }

  .new-btn {
    background-color: #fff;
    border-radius: 2px;
    width: 100%;
    height: 188px;
  }

  .meta-content {
    position: relative;
    overflow: hidden;
    text-overflow: ellipsis;
    display: -webkit-box;
    height: 64px;
    -webkit-line-clamp: 3;
    -webkit-box-orient: vertical;
  }
</style>
