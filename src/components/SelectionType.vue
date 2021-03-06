<template>
  <span
    v-click-outside="closeDropdown"
    :class="{ 'sm:mb-4': !inBanner }"
    class="SelectionType w-full sm:w-auto sm:mr-10"
  >
    <div class="flex sm:hidden w-full">
      <span
        :class="`bg-${backgroundColor} text-${secondaryTextColor}`"
        class="flex items-center rounded-l py-4 px-6 text-xs"
      >
        {{ $t("COMMON.TYPE") }}
      </span>

      <span
        :class="`border-${backgroundColor} text-${primaryTextColor}`"
        class="flex flex-1 items-center justify-between border rounded-r cursor-pointer p-4"
        @click="toggleDropdown"
      >
        <span class="font-bold">
          {{ $t(`TRANSACTION.TYPES.${transactionType.key}`) }}
        </span>

        <SvgIcon :class="{ 'rotate-180': isOpen }" name="caret" view-box="0 0 16 16" />
      </span>

      <ul v-show="isOpen" class="SelectionType--options inset-x-0 mt-10">
        <li v-for="type in types" :key="type.key">
          <div class="dropdown-button" @click="filterTransactions(type)">
            {{ $t(`TRANSACTION.TYPES.${type.key}`) }}
          </div>
        </li>
      </ul>
    </div>

    <div class="hidden sm:block">
      <span :class="[inBanner ? bannerClasses : 'text-theme-text-thead']" class="block mb-2 text-xs">
        {{ $t("COMMON.TYPE") }}
      </span>

      <span
        :class="`border-${backgroundColor} text-${primaryTextColor}`"
        class="flex items-center cursor-pointer"
        @click="toggleDropdown"
      >
        <span class="mr-1 md:whitespace-no-wrap">{{ $t(`TRANSACTION.TYPES.${transactionType.key}`) }}</span>

        <SvgIcon :class="{ 'rotate-180': isOpen }" name="caret" view-box="0 0 16 16" />
      </span>

      <ul v-show="isOpen" class="SelectionType--options right-0 mt-2">
        <li v-for="type in types" :key="type.key">
          <div class="dropdown-button" @click="filterTransactions(type)">
            {{ $t(`TRANSACTION.TYPES.${type.key}`) }}
          </div>
        </li>
      </ul>
    </div>
  </span>
</template>

<script lang="ts">
import { Component, Prop, Vue } from "vue-property-decorator";
import { DefaultTransaction, MarketplaceTransaction } from "@/enums";
import { ITransactionType } from "@/interfaces";

@Component
export default class SelectionType extends Vue {
  @Prop({ required: false, default: false }) public inBanner: boolean;
  private types: ITransactionType[] = [
    { key: "ALL", type: -1 },
    { key: "TRANSFER", type: DefaultTransaction.TRANSFER, typeGroup: 1 },
    { key: "SECOND_SIGNATURE", type: DefaultTransaction.SECOND_SIGNATURE, typeGroup: 1 },
    { key: "DELEGATE_REGISTRATION", type: DefaultTransaction.DELEGATE_REGISTRATION, typeGroup: 1 },
    { key: "VOTE", type: DefaultTransaction.VOTE, typeGroup: 1 },
    { key: "MULTI_SIGNATURE", type: DefaultTransaction.MULTI_SIGNATURE, typeGroup: 1 },
    { key: "IPFS", type: DefaultTransaction.IPFS, typeGroup: 1 },
    { key: "MULTI_PAYMENT", type: DefaultTransaction.MULTI_PAYMENT, typeGroup: 1 },
    { key: "DELEGATE_RESIGNATION", type: DefaultTransaction.DELEGATE_RESIGNATION, typeGroup: 1 },
    { key: "TIMELOCK", type: DefaultTransaction.TIMELOCK, typeGroup: 1 },
    { key: "TIMELOCK_CLAIM", type: DefaultTransaction.TIMELOCK_CLAIM, typeGroup: 1 },
    { key: "TIMELOCK_REFUND", type: DefaultTransaction.TIMELOCK_REFUND, typeGroup: 1 },
    { key: "BUSINESS_REGISTRATION", type: MarketplaceTransaction.BUSINESS_REGISTRATION, typeGroup: 2 },
    { key: "BUSINESS_RESIGNATION", type: MarketplaceTransaction.BUSINESS_RESIGNATION, typeGroup: 2 },
    { key: "BUSINESS_UPDATE", type: MarketplaceTransaction.BUSINESS_UPDATE, typeGroup: 2 },
    { key: "BRIDGECHAIN_REGISTRATION", type: MarketplaceTransaction.BRIDGECHAIN_REGISTRATION, typeGroup: 2 },
    { key: "BRIDGECHAIN_RESIGNATION", type: MarketplaceTransaction.BRIDGECHAIN_RESIGNATION, typeGroup: 2 },
    { key: "BRIDGECHAIN_UPDATE", type: MarketplaceTransaction.BRIDGECHAIN_UPDATE, typeGroup: 2 },
  ];
  private transactionType: ITransactionType = { key: "ALL", type: -1 };
  private selectOpen: boolean = false;

  get isOpen() {
    return this.selectOpen;
  }

  get backgroundColor() {
    return this.inBanner ? "none" : "theme-page-background";
  }

  get primaryTextColor() {
    return this.inBanner ? "white" : "theme-text-primary";
  }

  get secondaryTextColor() {
    return this.inBanner ? "grey" : "theme-text-secondary";
  }

  get bannerClasses() {
    return `bg-${this.backgroundColor} text-${this.secondaryTextColor}`;
  }

  public created() {
    const savedType = localStorage.getItem("transactionType");
    this.transactionType = savedType ? JSON.parse(savedType) : { key: "ALL", type: -1 };
  }

  private filterTransactions(type: ITransactionType) {
    this.closeDropdown();
    this.setTransactionType(type);
    this.$emit("change", type);
  }

  private setTransactionType(type: ITransactionType) {
    localStorage.setItem("transactionType", JSON.stringify(type));
    this.transactionType = type;
  }

  private closeDropdown() {
    this.selectOpen = false;
  }

  private toggleDropdown() {
    this.selectOpen = !this.selectOpen;
  }
}
</script>

<style scoped>
.SelectionType {
  @apply .flex .relative .z-20;
}

.SelectionType--options {
  max-height: 15rem;
  @apply .absolute .bg-theme-content-background .shadow-theme .rounded .border .overflow-hidden .text-sm .overflow-y-auto;
}
</style>
