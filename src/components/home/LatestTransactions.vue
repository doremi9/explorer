<template>
  <div>
    <loader :data="transactions">
      <div class="hidden sm:block">
        <table-transactions :transactions="transactions" />
      </div>
      <div class="sm:hidden">
        <table-transactions-mobile :transactions="transactions" />
      </div>
      <div class="mx-5 sm:mx-10 mt-5 md:mt-10 flex flex-wrap">
        <router-link :to="{ name: 'transactions', params: { page: 2 } }" tag="button" class="show-more-button">
          {{ $t("Show more") }}
        </router-link>
      </div>
    </loader>
  </div>
</template>

<script type="text/ecmascript-6">
import TransactionService from '@/services/transaction'

export default {
  props: {
    transactionType: {
      type: Number,
      required: true
    }
  },

  data: () => ({
    transactions: null
  }),

  watch: {
    async transactionType() {
      this.transactions = null
      await this.getTransactions()
    }
  },

  async mounted() {
    await this.prepareComponent()
  },

  methods: {
    async prepareComponent() {
      await this.getTransactions()

      this.$store.watch(state => state.network.height, value => this.getTransactions())
    },

    async getTransactions() {
      const { meta, data } = await TransactionService.filterByType(1, this.transactionType)
      this.transactions = data
    }
  }
}
</script>
