<template>
  <div class="fl card" @click.prevent="$router.push({name: 'token-id', params: { id: card.id }})">
    <div class="fl cards-content">
      <div class="card-info">
        <div class="token-cover">
          <c-token-popover :token-id="Number(card.id)">
            <avatar :src="cover" size="45px" />
          </c-token-popover>
        </div>
        <div class="token-info">
          <div>
            <h2 class="card-info-symbol">
              {{ card.symbol || $t('not') }}
            </h2>
          </div>
          <p class="card-info-name">
            {{ card.name || $t('not') }}
          </p>
          <p class="card-info-name brief">
            {{ card.brief || $t('not') }}
          </p>
        </div>
      </div>
      <div class="card-data">
        <div class="card-data-column">
          <p class="card-data-amount">
            <span>{{ $t('exchange-price') }}:</span> {{ unitPrice }} CNY
          </p>
        </div>
        <div class="card-data-column">
          <p class="card-data-amount">
            <span>{{ $t('token.liquidGold') }}:</span>  {{ cnyReserve }} CNY
          </p>
        </div>
        <div class="card-data-column">
          <p class="card-data-amount">
            <span>{{ $t('token.turnover24h') }}:</span> {{ exchangeAmount }} CNY
          </p>
        </div>
      </div>
      <div class="card-user">
        <div class="fl ac user" @click.stop="jumpUser(card.uid)">
          <c-user-popover :user-id="Number(card.uid)">
            <c-avatar
              :src="coverUser"
              :recommend-author="card.user_is_recommend === 1"
              :token-user="card.user_is_token === 1"
            />
          </c-user-popover>
          <span class="card-username">{{ card.nickname || card.username }}</span>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import avatar from '@/components/avatar/index.vue'
import { precision } from '@/utils/precisionConversion'

export default {
  components: {
    avatar
  },
  props: {
    card: {
      type: Object,
      required: true
    }
  },
  computed: {
    cover() {
      return this.card.logo ? this.$ossProcess(this.card.logo, { h: 60 }) : ''
    },
    coverUser() {
      return this.card.avatar ? this.$ossProcess(this.card.avatar, { h: 60 }) : ''
    },
    cnyReserve() {
      const tokenamount = precision(this.card.liquidity || 0, 'CNY', this.card.decimals)
      return this.$publishMethods.formatDecimal(tokenamount, 4)
    },
    unitPrice() {
      const amount = this.card.amount || 0
      const liquidity = this.card.liquidity || 0

      if (amount === 0) return 0

      return (liquidity / amount).toFixed(this.card.decimals)
    },
    exchangeAmount() {
      const tokenamount = precision(this.card.exchange_amount || 0, 'CNY', this.card.decimals)
      return this.$publishMethods.formatDecimal(tokenamount, 4)
    },
    friendlyDate() {
      const time = this.moment(this.card.create_time)
      return this.$utils.isNDaysAgo(2, time) ? time.format('MMMDo HH:mm') : time.fromNow()
    },
  },
  methods: {
    // 跳转到用户页面
    jumpUser(uid) {
      try {
        if (window) {
          window.open(`/user/${uid}`, '_blank')
        } else {
          throw new Error('not window')
        }
      } catch (error) {
        this.$router.push({name: 'user-id', params: { id: uid }})
      }
    }
  }
}
</script>
<style lang="less" scoped>
.card {
  padding: 20px 0;
  border-bottom: 1px solid #dbdbdb;
  color: #000;
  text-decoration: none;
  overflow: hidden;
  cursor: pointer;
  // &:nth-last-child(1) {
  //   border-bottom: 1px solid #dbdbdb;
  // }
}
.cards-content {
  flex: 1;
}

.card-info {
  width: 26%;
  &-symbol {
    font-size: 20px;
    font-weight: 500;
    color: @black;
    line-height: 28px;
    padding: 0;
    margin: 0;
  }
  &-name {
    font-size: 14px;
    font-weight: 400;
    color: @gray;
    line-height: 20px;
    padding: 0;
    margin: 4px 0 0 0;
    &.brief {
      display: -webkit-box;
      -webkit-box-orient: vertical;
      -webkit-line-clamp: 2;
      overflow: hidden;
    }
  }
  .token-cover {
    float: left;
    width: 35%;
  }
  .token-info {
    float: left;
    width: 65%;
  }
}

.card-data {
  width: 48%;

  &-column {
    float: left;
    width: 33.3%;
    height: 100%;
    display: flex;
    align-items: center;
    justify-content: center;
  }

  &-amount {
    text-align: center;
    font-size: 16px;
    margin: 0;
    span {
      display: none;
    }
  }
}

.card-user {
  width: 26%;
  display: flex;
  justify-content: center;
  align-items: flex-end;
  flex-direction: column;
  .user {
    cursor: pointer;
  }
}
.card-username {
  margin-left: 6px;
  font-size: 14px;
  font-weight: 400;
  color: @black;
  line-height: 20px;
  display: -webkit-box;
  -webkit-box-orient: vertical;
  -webkit-line-clamp: 1;
  overflow: hidden;
  word-break: break-all;
  max-width: 250px;
}
.card-user-icon {
  display: flex;
  flex: 1;
  align-items: flex-end;
}
.card-create-time {
  margin: auto 0 0 auto;
}

// <880
@media screen and (max-width: 880px) {
  .card-user {
    display: none;
  }
  .card-info {
    width: 35%;
  }
  .card-data {
    width: 65%;
  }
}

// < 580
@media screen and (max-width: 580px) {
  .token-cover {
    /deep/ .g-avatar {
      width: 30px !important;
      height: 30px !important;
    }
  }
  .cards-content {
    display: block;
    .card-info {
      width: 100%;
      .token-cover {
        width: 15%;
        min-width: 30px;
      }
      .token-info {
        width: 85%;
      }
    }
    .card-info-symbol {
      font-size: 16px;
      line-height: 20px;
    }
    .card-info-name {
      font-size: 12px;
    }
    .card-data {
      width: 100%;
      margin-top: 10px;
      margin-left: 15%;
      .card-data-column {
        float: none;
        width: 100%;
        justify-content: flex-start;
        .card-data-amount {
          font-size:14px;
          font-weight:400;
          color:rgba(0,0,0,1);
          line-height:20px;
          margin: 10px 0 0 0;
        }
        &.circle {
          display: none;
        }
      }
      .card-data-amount {
        line-height: 1.5;
        text-align: left;
        span {
          display: inline;
        }
      }
    }
  }
}
</style>
