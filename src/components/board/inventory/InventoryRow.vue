<template>
  <div class="inventory-row-wrapper">
    <div
      :class="{ 'inventory-row__basic--no-border': extraInfoShown }"
      class="inventory-row__basic"
    >
      <div class="inventory-row__basic__model">{{ modelName }}</div>
      <div class="inventory-row__basic__language">{{ language.name }}</div>
      <div class="inventory-row__basic__location">{{ location }}</div>
      <div class="inventory-row__basic__owner">{{ createdBy }}</div>
      <div class="inventory-row__basic__session">
        <div
          :class="
            hasCollaborationSession
              ? 'inventory-row__basic__session__status-ring--on'
              : 'inventory-row__basic__session__status-ring--off'
          "
          class="inventory-row__basic__session__status-ring"
        ></div>
        <div
          :class="
            hasCollaborationSession
              ? 'inventory-row__basic__session__status--on'
              : 'inventory-row__basic__session__status--off'
          "
          class="inventory-row__basic__session__status"
        ></div>
      </div>
      <div
        :class="{ 'inventory-row__basic__arrow--flipped': extraInfoShown }"
        class="inventory-row__basic__arrow"
        @click="toggleExtraInfo"
      >
        <div class="iconfont">&#xe671;</div>
      </div>
      <div class="inventory-row__basic__actions">
        <ul>
          <li
            :class="{
              'inventory-row__basic__actions__button--disabled': !validModel,
            }"
            class="inventory-row__basic__actions__button inventory-row__basic__actions__button__view"
            @click="viewModel"
          >
            VIEW
          </li>
        </ul>
      </div>
    </div>
    <div v-show="extraInfoShown" class="inventory-row__extra">
      <ul>
        <li>
          <div class="inventory-row__extra__key">uri:</div>
          <div class="inventory-row__extra__value">{{ model.uri }}</div>
        </li>
        <li>
          <div class="inventory-row__extra__key">Supported Editors:</div>
          <ul class="inventory-row__extra__value">
            <li v-for="item in supportedEditors" :key="item">
              {{ item.name }}
            </li>
          </ul>
        </li>
      </ul>
      <div
        v-if="hasCollaborationSession"
        class="inventory-row__extra__participants"
      >
        <table class="model__table table table-hover">
          <thead>
            <tr>
              <th>Collaboration Session Participant</th>
              <th>Role</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="user in collaborationSession.participants" :key="user">
              <td>{{ getUserName(user.user.$ref) }}</td>
              <td>{{ user.role || "COLLABORATOR" }}</td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>

<script>
import { validate } from "../../../utils/request";
import { getLanguage } from "../../../utils/tools";
import { mapGetters } from "vuex";

export default {
  name: "InventoryRow",
  props: {
    modelId: String,
  },
  data() {
    return {
      extraInfoShown: false,
      validModel: false,
      collaborationSession: {},
    };
  },
  computed: {
    ...mapGetters(["modelInventory"]),

    model() {
      const model = this.modelInventory.model.filter(
        (model) => model.$id === this.modelId
      )[0];

      return model;
    },
    modelName() {
      return this.model.name;
    },
    language() {
      return getLanguage(this.modelInventory, this.model);
    },
    supportedEditors() {
      return this.language.supportedEditors;
    },
    location() {
      return this.model.location;
    },
    createdBy() {
      return this.model.createdBy || "/";
    },
    hasCollaborationSession() {
      const sessions = this.modelInventory.collaborationSessions;
      const session = sessions.filter(
        (session) => session.has.$ref === this.model.$id
      );

      return session.length !== 0;
    },
  },
  methods: {
    viewModel() {
      if (this.validModel) {
        this.$emit("viewModel");
      } else return;
    },
    toggleExtraInfo() {
      this.extraInfoShown = !this.extraInfoShown;
    },
    getUserName(userID) {
      const users = this.modelInventory.users;
      const user = users.filter((user) => user.$id === userID);
      return user[0].name;
    },
  },
  async mounted() {
    if (this.hasCollaborationSession) {
      this.collaborationSession =
        this.modelInventory.collaborationSessions.filter(
          (session) => session.has.$ref === this.model.$id
        )[0];
    }
    let result = await validate(`/validation/?modeluri=${this.modelName}.xmi`);
    this.validModel = result.data !== "Model not found!";
  },
};
</script>

<style lang="scss">
@import "../../../assets/base.scss";
@import "../../../assets/iconfont.css";

.inventory-row-wrapper {
  @include flexCenter;
  flex-direction: column;
  width: 100%;
}

.inventory-row__basic {
  display: flex;
  flex-direction: row;
  align-items: center;
  width: 90%;
  height: 70px;
  border-bottom: 1px solid $horizontal-line-color;

  &--no-border {
    border: none;
  }

  &__model,
  &__language,
  &__location,
  &__owner {
    width: 15%;
  }

  &__session {
    display: flex;
    position: relative;
    width: 10%;
    height: 100%;

    &__status {
      position: absolute;
      top: 28px;
      left: 3px;
      width: 15px;
      height: 15px;
      border-radius: 50%;

      &--on {
        background-color: green;
      }

      &--off {
        background-color: red;
      }
    }

    &__status-ring {
      position: absolute;
      left: 0px;
      top: 25px;
      width: 21px;
      height: 21px;
      border-radius: 50%;

      &--on {
        border: 2px solid rgba(0, 128, 0, 0.5);
      }

      &--off {
        border: 2px solid rgba(255, 0, 0, 0.5);
      }
    }
  }

  &__arrow {
    @include flexCenter;
    width: 10%;

    .iconfont {
      @include flexCenter;
      width: 1.5rem;
      height: 1.5rem;
      font-size: 1.5rem;
      color: $disabled-button-font-color;
      transform: rotate(180deg);
      transition: all 0.2s ease;

      &:hover {
        color: $theme-color;
        cursor: pointer;
      }
    }

    &--flipped {
      .iconfont {
        transform: rotate(0deg);
        color: red;
      }
    }
  }

  &__actions {
    width: 20%;

    ul {
      @include flexCenter;
    }

    &__button {
      @include flexCenter;
      width: 70px;
      height: 35px;
      background-color: $secondary-theme-color;
      font-size: 0.9rem;
      font-weight: bold;

      &:hover {
        cursor: pointer;
        background-color: darken($secondary-theme-color, 10%);
      }

      &__view {
        width: 100%;
      }

      &--disabled {
        background-color: lighten($secondary-theme-color, 20%);
        color: $disabled-button-font-color;

        &:hover {
          cursor: pointer;
          background-color: lighten($secondary-theme-color, 20%);
        }
      }
    }
  }
}

.inventory-row__extra {
  @include flexCenter;
  flex-direction: column;
  width: 100%;
  overflow: hidden;
  border-left: 10px solid $theme-color;
  background-color: $extra-info-backgroun-color;
  box-shadow: inset 1px 2px 3px rgba(0, 0, 0, 0.25);

  ul {
    width: 90%;
  }

  li {
    display: flex;
    align-items: center;
    margin: 5px 0;
  }

  &__key {
    width: 15%;
  }

  &__participants {
    margin-top: 1rem;
    width: 90%;
  }
}

.inventory-row__extra__value {
  display: flex;

  li {
    padding: 0 10px;
    border-right: 1px solid $horizontal-line-color;

    &:last-child {
      border-right: none;
    }
  }
}
</style>
