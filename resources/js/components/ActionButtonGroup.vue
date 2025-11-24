<template>
  <div class="flex justify-end items-center mr-3 ml-1">
    <invisible-actions-dropdown
      class="mr-2"
      v-if="shouldShowInvisibleActions"
      :actions="invisibleActions"
      :show-arrow="showInvisibleActionsArrow"
      :icon-type="invisibleActionsIcon"
      @dropdown-link-click="handleClick"
    ></invisible-actions-dropdown>

    <template v-for="action in visibleActions">
      <action-button
          class="mr-3"
          v-if="shouldShowActions || action.standalone"
          :key="action.uriKey"
          :action="action"
          @action-button-clicked="handleClick"
      ></action-button>
    </template>

    <!-- Confirm Action Modal -->
    <component
      v-if="confirmActionModalOpened"
      class="text-left"
      :show="confirmActionModalOpened"
      :is="selectedAction.component"
      :working="working"
      :selected-resources="selectedResources"
      :resource-name="resourceName"
      :action="selectedAction"
      :errors="errors"
      @confirm="executeAction"
      @close="closeConfirmationModal"
    />

    <component
      :is="actionResponseData.modal"
      @close="closeActionResponseModal"
      v-if="showActionResponseModal"
      :show="showActionResponseModal"
      :data="actionResponseData"
    />
  </div>
</template>

<script>
import { Errors } from 'laravel-nova'
import InteractsWithResourceInformation from '@/mixins/InteractsWithResourceInformation'

export default {
  mixins: [InteractsWithResourceInformation],

  props: ['shouldShowActions', 'resourceName', 'resourceId', 'actions', 'endpoint', 'actionQueryString', 'selectedResources'],

  data: () => ({
    visibleActionsDefaultLimit: 3,
    actionsList: [],
    confirmActionModalOpened: false,
    working: false,
    errors: new Errors(),
    selectedActionKey: '',
    showActionResponseModal: false,
    actionResponseData: {},
  }),

  watch: {
    actions(newActions) {
      this.actionsList = newActions.filter((action) => action.hasOwnProperty('detachedAction'));
    },
  },

  computed: {
    detachedActions() {
      return this.actionsList.filter(action => action.detachedAction || false)
    },

    visibleActionsLimit() {
      return this.resourceInformation?.visibleActionsLimit ?? this.visibleActionsDefaultLimit
    },

    visibleActions() {
      return this.visibleActionsLimit == 0 ? [] : this.detachedActions.slice(0, this.visibleActionsLimit)
    },

    invisibleActions() {
      return this.detachedActions.slice(this.visibleActionsLimit)
    },

    shouldShowInvisibleActions() {
      return this.detachedActions.length > this.visibleActionsLimit
    },

    showInvisibleActionsArrow() {
      return this.resourceInformation?.showInvisibleActionsArrow ?? false
    },

    invisibleActionsIcon() {
      return this.resourceInformation?.invisibleActionsIcon ?? 'ellipsis-horizontal'
    },

    selectedAction() {
      if (this.selectedActionKey) {
        return this.actionsList.find(a => a.uriKey === this.selectedActionKey)
      }
      return {}
    },
  },

  methods: {
    handleClick(action) {
      if (action.authorizedToRun !== false) {
        this.selectedActionKey = action.uriKey
        this.determineActionStrategy()
      }
    },

    determineActionStrategy() {
      if (this.selectedAction.withoutConfirmation) {
        this.executeAction()
      } else {
        this.openConfirmationModal()
      }
    },

    openConfirmationModal() {
      this.errors = new Errors()
      this.confirmActionModalOpened = true
    },

    closeConfirmationModal() {
      this.confirmActionModalOpened = false
    },

    closeActionResponseModal() {
      this.showActionResponseModal = false
      this.$emit('actionExecuted')
    },

    executeAction() {
      this.working = true
      Nova.$progress.start()

      const actionEndpoint = this.endpoint || `/nova-api/${this.resourceName}/action`
      const formData = new FormData()

      if (this.selectedResources === 'all') {
        formData.append('resources', 'all')
      } else {
        this.selectedResources.forEach(resource => {
          const resourceId = typeof resource === 'object' ? resource.id?.value : resource
          formData.append('resources[]', resourceId)
        })
      }

      this.selectedAction.fields?.forEach(field => field.fill(formData))

      Nova.request({
        method: 'post',
        url: actionEndpoint,
        params: { action: this.selectedActionKey, ...this.actionQueryString },
        data: formData,
      })
        .then(response => {
          this.closeConfirmationModal()
          this.handleActionResponse(response.data)
        })
        .catch(error => {
          if (error.response?.status >= 400 && error.response?.status < 500) {
            this.errors = new Errors(error.response.data.errors || {})
            Nova.error(Nova.__('There was a problem executing the action.'))
          }
        })
        .finally(() => {
          this.working = false
          Nova.$progress.done()
        })
    },

    handleActionResponse(data) {
      if (data.message) {
        Nova.success(data.message)
      }

      if (data.modal) {
        this.actionResponseData = data.modal
        this.showActionResponseModal = true
      } else {
        this.$emit('actionExecuted')
      }

      Nova.$emit('action-executed')
    },
  },

  created() {
    this.actionsList = this.actions
  }
}
</script>
