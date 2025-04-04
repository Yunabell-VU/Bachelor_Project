<template>
  <BoardLayout :titleName="'Sessions'">
    <template #content>
      <div class="sessions-wrapper">
        <div
          v-for="session in modelInventory.collaborationSessions"
          :key="session"
        >
          <div>
            <div class="sessions-title-info">
              <span>Model : {{ getModelName(session.has.$ref) }}</span>
              <span>Collaboration Session (id : {{ session.id }})</span>
            </div>
            <div class="session-time-info">
              <span
                >Start Time: {{ getTimeFormat(session.startTime) || "/" }}</span
              >
              <span>End Time: {{ getTimeFormat(session.endTime) || "/" }}</span>
            </div>
            <table class="sessions__table table table-hover">
              <thead>
                <tr>
                  <th>Participant</th>
                  <th>Role</th>
                </tr>
              </thead>
              <tbody>
                <tr
                  v-for="participant in session.participants"
                  :key="participant"
                >
                  <td>
                    {{ getUserName(participant.user.$ref) }}
                  </td>
                  <td>
                    {{ getRoleValue(participant) }}
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>
      </div>
    </template>
  </BoardLayout>
</template>

<script>
import BoardLayout from "../layout/BoardLayout.vue";
import { mapGetters } from "vuex";
import { timeConverter } from "../../utils/tools";

export default {
  name: "Sessions",
  components: { BoardLayout },
  data() {
    return {
      isEdit: false,
      participants: [],
    };
  },
  computed: {
    ...mapGetters(["modelInventory", "inventoryTemplate", "currentUser"]),
  },
  methods: {
    getRoleValue: (participant) => participant.role || "COLLABORATOR",
    getUserName(userID) {
      const users = this.modelInventory.users;
      const user = users.filter((user) => user.$id === userID);
      return user[0].name;
    },
    getModelName(modelID) {
      const models = this.modelInventory.model;
      if (models) {
        const model = models.filter((model) => model.$id === modelID);

        if (model.length > 0) {
          return model[0].name;
        }
      }

      return "Model Deleted";
    },
    getTimeFormat(timeStamp) {
      return timeConverter(timeStamp);
    },
  },
  mounted() {},
};
</script>

<style lang="scss">
@import "../../assets/base.scss";
@import "../../assets/iconfont.css";

.sessions-create-new-button {
  @include flexCenter;
  width: 70px;
  height: 35px;
  background-color: $create-button-color;
  color: $theme-font-color;
  font-size: 1rem;
  font-weight: bold;

  &:hover {
    cursor: pointer;
    background-color: darken($create-button-color, 10%);
  }
}

.sessions-wrapper {
  display: flex;
  flex-direction: column;
  padding: 0px 5%;
  width: 100%;
}

.sessions-title-info {
  @include flexSpaceBetween;
  margin-top: 2rem;
  padding: 0px 10px;
  width: 100%;
  height: 60px;
  background-color: $theme-color;
  color: $theme-font-color;

  span {
    font-weight: bold;
  }
}

.session-time-info {
  @include flexSpaceBetween;
  margin: 10px 5px;

  span {
    font-weight: 600;
  }
}
</style>
