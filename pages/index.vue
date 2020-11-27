<template>
  <div class="container">
    <TrollFace />
    <form onsubmit="return false" @submit-prevent="createAndSend()">
      <p v-if="error">
        {{ error }}
      </p>
      <div class="field">
        <label class="label">ربات مورد نظرت چیه؟</label>
        <div class="control">
          <div class="select">
            <select v-model="bot">
              <option>Loritta</option>
              <option>UnbeliavaBoat</option>
              <option>MEE6</option>
              <option>Custom</option>
            </select>
          </div>
        </div>
      </div>
      <div v-if="bot" class="fancyFields">
        <div class="field">
          <label class="label">Webhook URL</label>
          <div class="control">
            <input v-model="url" class="input" type="text" placeholder="لینک وب هوک" required="true">
          </div>
        </div>
        <div v-if="bot === `Custom`" class="optional">
          <div class="field">
            <label class="label">Webhook Name</label>
            <div class="control">
              <input v-model="name" class="input" type="text" placeholder="نام وب هوک" required="true">
            </div>
          </div>
          <div class="field">
            <label class="label">Webhook Avatar</label>
            <div class="control">
              <input v-model="avatar" class="input" type="url" placeholder="آواتار وب هوک" required="true">
            </div>
          </div>
          <div class="field">
            <label class="label">Message</label>
            <div class="control">
              <input v-model="message" class="input" type="text" placeholder="پیام" required="true">
            </div>
          </div>
        </div>
        <div v-if="bot === `MEE6`" class="mee6Fields">
          <div class="field">
            <label class="label">User Avatar</label>
            <div class="control">
              <input v-model="UserAvatar" class="input" type="url" placeholder="User Avatar" required="true">
            </div>
          </div>
          <div class="field">
            <label class="label">Reason</label>
            <div class="control">
              <input v-model="reason" class="input" type="text" placeholder="دلیل" required="true">
            </div>
          </div>
        </div>
        <div class="field" v-if="bot != `Custom`">
          <label v-if="bot != `MEE6`" class="label">User ID</label>
          <label v-else class="label">User ID (Exemple: ali_s#0107)</label>
          <div v-if="bot != `MEE6`" class="control">
            <input v-model="userId" class="input" type="number" placeholder="آیدی موردنظر" required="true">
          </div>
          <div v-else class="control">
            <input v-model="userTag" class="input" type="text" placeholder="Username#Tag" required="true">
          </div>
        </div>
        <button class="button" type="submit" @click="createAndSend">
          Send
        </button>
      </div>
      <p>💖 Edited By <a href="https://github.com/ali0sam">ali0sam</a></p>
      <p>🛠 Discord <a href="https://github.com/ali0sam">ali_s#0107</a></p>
    </form>
  </div>
</template>

<script lang="ts">
import Vue from 'vue'
import { Webhook, MessageBuilder } from 'discord-webhook-node'
import { Constants } from '../utils/Constants'
export default Vue.extend({
  data () {
    return {
      url: '',
      name: '',
      avatar: '',
      userId: '',
      bot: '',
      reason: '',
      message: '',
      UserAvatar: '',
      userTag: '',
      error: ''
    }
  },
  methods: {
    async createAndSend () {
      try {
        if (this.bot === 'MEE6') {
          if (!this.UserAvatar || !this.userTag) {
            return
          }
        } else if (this.bot === 'UnbeliavaBoat' || this.bot === 'Loritta') {
          if (!this.userId) {
            return
          }
        } else if (this.bot === 'Custom') {
          if (!this.avatar || !this.name || !this.message) {
            return
          }
        }
        const webhook = new Webhook(this.url)
        switch (this.bot) {
          case 'Loritta': {
            webhook.setAvatar(Constants.LORITTA_ICON)
            webhook.setUsername('Loritta')
            await webhook.send(Constants.LORITTA_MESSAGE.replace('$USERID', this.userId))
            break
          }
          case 'UnbeliavaBoat': {
            webhook.setAvatar(Constants.UNBELIEVA_ICON)
            webhook.setUsername('UnbeliavaBoat')
            await webhook.send(Constants.UNBELIEVA_MESSAGE.replace('$USERID', this.userId).replace('$CASE', '`Case #9458`'))
            break
          }
          case 'MEE6': {
            webhook.setAvatar(Constants.MEE6_ICON)
            webhook.setUsername('MEE6')
            const meeEmbed = new MessageBuilder()
            meeEmbed.setAuthor(Constants.MEE6_EMBED.title.replace('$USERID', this.userTag), this.UserAvatar)
            meeEmbed.setDescription(Constants.MEE6_EMBED.description.replace('$REASON', this.reason))
            await webhook.send(meeEmbed)
            break
          }
          case 'Custom': {
            webhook.setAvatar(this.avatar)
            webhook.setUsername(this.name)
            await webhook.send(this.message)
            break
          }
        }
        // await webhook.send(`<:_check:634831134571888641> \`Case #9458\` <@${this.userId}> has been banned.`)
        this.error = 'Sucesso!'
      } catch (e) {
        this.error = e
      }
    }
  }
})

</script>

<style>
.container {
  margin: 0 auto;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
}
.button {
  background:#40444b;
  color:white;
}
.label {
  background:#40444b;
  color:white;
  border-radius:30px;
}


body {
  background:#36393E;
}


</style>
