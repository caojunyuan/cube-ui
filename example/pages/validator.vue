<template>
  <cube-page type="validator-view" title="Validator" desc="">
    <div slot="content">
      <div class="validator-item">
        <cube-input v-model="text1" placeholder="E-mail"></cube-input>
        <cube-validator ref="validator1" v-model="isValid[0]" :model="text1" :rules="rules1" :messages="messages1" :immediate="immediate"></cube-validator>
      </div>
      <div class="validator-item">
        <cube-validator ref="validator2" v-model="isValid[1]" :model="text2" :rules="rules2" :messages="messages2" :immediate="immediate">
          <cube-input v-model="text2" placeholder="component name"></cube-input>
          <div slot="message" class="custom-msg" slot-scope="props">
            <div v-if="(props.dirty || props.validated) && !isValid[1]">
              <i class="dd-cubeic-important"></i> {{ props.message }}
              <div>
                <span v-for="(item, index) in Object.values(props.result)" :key="index" v-if="item.inValid">{{ item.message + ' ' }}</span>
              </div>
            </div>
          </div>
        </cube-validator>
      </div>
      <div class="validator-item">
        <cube-validator ref="validator3" v-model="isValid[2]" :model="text3" :rules="rules3" :immediate="immediate">
          <cube-input v-model="text3" placeholder="odd"></cube-input>
        </cube-validator>
      </div>
      <div class="validator-item">
        <cube-checkbox-group v-model="checkList" :horizontal="true">
          <cube-checkbox label="1">1</cube-checkbox>
          <cube-checkbox label="2">2</cube-checkbox>
          <cube-checkbox label="3">3</cube-checkbox>
        </cube-checkbox-group>
        <cube-validator ref="validator4" v-model="isValid[3]" :model="checkList" :rules="rules4" :immediate="immediate"></cube-validator>
      </div>

      <div class="validator-item">
        <cube-rate v-model="rate"></cube-rate>
        <cube-validator ref="validator5" v-model="isValid[4]" :model="rate" :rules="rules5" :immediate="immediate"></cube-validator>
      </div>
      <cube-button @click="submit">Submit</cube-button>
    </div>
  </cube-page>
</template>

<script type="text/ecmascript-6">
  import CubePage from '../components/cube-page.vue'

  // Add or rewrite the build-in rule, type and message.
  import { Validator } from '../../src/module'

  export default {
    data() {
      return {
        immediate: false,
        text1: '',
        isValid: [true, true, true, true, true],
        rules1: {
          required: true,
          type: 'email',
          pattern: /didi.com$/,
          custom: (val) => {
            return val.length >= 12
          }
        },
        messages1: {
          pattern: 'The E-mail suffix need to be didi.com.',
          custom: 'The E-mail need contain at least 12 characters.'
        },
        text2: '',
        rules2: {
          type: 'string',
          pattern: /^cube-/,
          min: 8,
          max: 10
        },
        messages2: {
          pattern: 'Please start with "cube-"'
        },
        text3: '100',
        rules3: {
          type: 'number',
          odd: true
        },
        checkList: [],
        rules4: {
          required: true
        },
        rate: 0,
        rules5: {
          min: 1,
          max: 4
        }
      }
    },
    created() {
      Validator.setLanguage('en')
      Validator.addRule('odd', (val, config, type) => !config || Number(val) % 2 === 1)
      Validator.addMessage('odd', 'Please input odd.')
      Validator.addType('email', (val) => {
        return typeof val === 'string' && /^[a-z0-9_-]+@[a-z0-9_-]+(\.[a-z0-9_-]+)$/i.test(val)
      })
    },
    methods: {
      submit() {
        Object.keys(this.$refs).forEach((key) => {
          this.$refs[key].validate()
        })
        if (this.isValid.every(item => item)) {
          this.$createToast({
            type: 'correct',
            txt: 'Submited',
            time: 1000
          }).show()
        }
      }
    },
    components: {
      CubePage
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  .validator-item
    box-sizing: border-box
    min-height: 70px
    .custom-msg
      color: orange
</style>
