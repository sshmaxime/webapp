<template>
  <content-block :shadow="true" :title="title" :back-button="true" @back="back">
    <div v-if="!withdrawLiquidity">
      <pool-actions-add-v1 v-if="!pool.v2" :pool="pool" />
      <pool-actions-add-v2 v-else :pool="pool" />
    </div>

    <div v-else>
      <pool-actions-remove-v1 v-if="!pool.v2" :pool="pool" />
      <pool-actions-remove-v2 v-else :pool="pool" />
    </div>
  </content-block>
</template>

<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import { vxm } from "@/store/";
import ContentBlock from "@/components/common/ContentBlock.vue";
import { ViewRelay } from "@/types/bancor";
import PoolActionsAddV1 from "@/components/pool/PoolActionsAddV1.vue";
import PoolActionsAddV2 from "@/components/pool/PoolActionsAddV2.vue";
import PoolActionsRemoveV1 from "@/components/pool/PoolActionsRemoveV1.vue";
import PoolActionsRemoveV2 from "@/components/pool/PoolActionsRemoveV2.vue";

@Component({
  components: {
    PoolActionsRemoveV2,
    PoolActionsRemoveV1,
    PoolActionsAddV2,
    PoolActionsAddV1,
    ContentBlock
  }
})
export default class PoolActions extends Vue {
  withdrawLiquidity = false;
  detailMode: boolean | null = null;

  get title() {
    return (this.withdrawLiquidity ? "Remove" : "Add") + " Liquidity";
  }

  get version() {
    if (this.detailMode === null) return this.pool.v2 ? 2 : 1;
    else return null;
  }

  back() {
    this.$router.go(-1);
  }

  get pool(): ViewRelay {
    return vxm.bancor.relay(this.$route.params.account);
  }

  created() {
    this.withdrawLiquidity = this.$route.params.poolAction === "remove";
    if (this.withdrawLiquidity && !this.pool.v2) this.detailMode = false;
  }
}
</script>

<style scoped lang="scss"></style>
