<template>
  <div class="structure-wrapper">
    <div v-for="eenum in enums" :key="eenum" class="enum-board">
      <div class="enum-board__title">{{ eenum.name }}</div>
      <div class="enum-board__content">
        <div
          v-for="literal in eenum.eLiterals"
          :key="literal"
          class="enum-board__content__literal"
        >
          <span class="enum-board__content__literal__name">
            {{ literal.name }}</span
          >
          <span class="enum-board__content__literal__value"
            >value: {{ literal.value || "/" }}</span
          >
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import BoardLayout from "../../layout/BoardLayout.vue";
import { mapGetters } from "vuex";

export default {
  name: "StructureEnum",
  components: { BoardLayout },
  props: {
    enums: Object,
  },
  data() {
    return {};
  },
  computed: {
    ...mapGetters(["modelInventory"]),
  },
  methods: {},
  mounted() {},
};
</script>

<style lang="scss">
@import "../../../assets/base.scss";

.structure-header-buttons {
  @include flexSpaceBetween;
  width: 200px;

  &__button {
    @include flexCenter;
    width: 70px;
    height: 35px;
    background-color: $secondary-theme-color;
    color: $theme-color;
    font-size: 1rem;
    font-weight: bold;

    &:hover {
      cursor: pointer;
      background-color: darken($secondary-theme-color, 10%);
    }

    &__view {
      background-color: $create-button-color;
      color: $theme-font-color;
    }
  }
}

.structure-wrapper {
  display: flex;
  flex-wrap: wrap;
  justify-content: flex-start;
  margin-top: 2rem;
  width: 100%;
}

.enum-board {
  flex: 1;
  margin: 0 100px 50px 0;
  width: calc((100% - 200px) / 3);
  min-width: calc((100% - 200px) / 3);
  max-width: calc((100% - 200px) / 3);
  border: 2px solid $theme-color;

  &:nth-child(3n) {
    margin-right: 0;
  }

  &__title {
    @include flexCenter;
    width: 100%;
    height: 40px;
    background-color: $theme-color;
    color: $theme-font-color;
    font-size: 1.1rem;
    font-weight: bold;
  }

  &__content {
    padding: 0 10px 10px 10px;
    &__literal {
      @include flexSpaceBetween;
      margin-top: 10px;
      border-bottom: 1px solid $theme-color;
      &:last-child {
        border: none;
      }

      &__value {
        color: $soft-font-color;
      }

      span {
        height: 2rem;
        font-weight: 600;
      }
    }
  }
}
</style>
