<script>
import { visitUrl, getParameterByName, updateHistory, removeParams } from '~/lib/utils/url_utility';
import { TYPENAME_WORK_ITEM } from '~/graphql_shared/constants';
import { convertToGraphQLId } from '~/graphql_shared/utils';
import CreateWorkItem from '../components/create_work_item.vue';
import CreateWorkItemCancelConfirmationModal from '../components/create_work_item_cancel_confirmation_modal.vue';
import {
  ROUTES,
  RELATED_ITEM_ID_URL_QUERY_PARAM,
  BASE_ALLOWED_CREATE_TYPES,
  WORK_ITEM_TYPE_NAME_EPIC,
  WORK_ITEM_TYPE_NAME_INCIDENT,
  WORK_ITEM_TYPE_NAME_ISSUE,
  WORK_ITEM_TYPE_NAME_TASK,
  WORK_ITEM_TYPE_ROUTE_WORK_ITEM,
  WORK_ITEM_TYPE_ROUTE_ISSUE,
} from '../constants';
import workItemRelatedItemQuery from '../graphql/work_item_related_item.query.graphql';
import { convertTypeEnumToName } from '../utils';

export default {
  name: 'CreateWorkItemPage',
  components: {
    CreateWorkItem,
    CreateWorkItemCancelConfirmationModal,
  },
  inject: ['isGroup'],
  props: {
    rootPageFullPath: {
      type: String,
      required: true,
    },
    workItemTypeEnum: {
      type: String,
      required: false,
      default: null,
    },
  },
  data() {
    // Generated by backend with add_related_issue query parameter
    const relatedId = document.querySelector('.params-add-related-issue')?.textContent.trim();
    const legacyAddRelatedIssueId = relatedId
      ? convertToGraphQLId(TYPENAME_WORK_ITEM, relatedId)
      : undefined;

    return {
      relatedItem: null,
      relatedItemId: getParameterByName(RELATED_ITEM_ID_URL_QUERY_PARAM) || legacyAddRelatedIssueId,
      isCancelConfirmationModalVisible: false,
      shouldDiscardDraft: false,
      workItemType: convertTypeEnumToName(this.workItemTypeEnum),
    };
  },
  apollo: {
    relatedItem: {
      query: workItemRelatedItemQuery,
      variables() {
        return {
          id: this.relatedItemId,
        };
      },
      skip() {
        return !this.relatedItemId;
      },
      update({ workItem }) {
        return {
          id: this.relatedItemId,
          reference: workItem.reference,
          type: workItem.workItemType.name,
          webUrl: workItem.webUrl,
        };
      },
      error() {
        // if we cannot find an item with the given id, ignore it and remove it from the url.
        updateHistory({ url: removeParams([RELATED_ITEM_ID_URL_QUERY_PARAM]), replace: true });
      },
    },
  },
  computed: {
    isEpic() {
      return this.workItemType === WORK_ITEM_TYPE_NAME_EPIC;
    },
    isIncident() {
      return this.workItemType === WORK_ITEM_TYPE_NAME_INCIDENT;
    },
    allowedWorkItemTypes() {
      if (
        [
          WORK_ITEM_TYPE_NAME_ISSUE,
          WORK_ITEM_TYPE_NAME_INCIDENT,
          WORK_ITEM_TYPE_NAME_TASK,
        ].includes(this.workItemType)
      ) {
        return BASE_ALLOWED_CREATE_TYPES;
      }

      return [];
    },
    isNewGroupWorkItem() {
      return !this.isEpic && this.isGroup;
    },
  },
  methods: {
    updateWorkItemType(type) {
      this.workItemType = type;
    },
    workItemCreated({ workItem, numberOfDiscussionsResolved }) {
      if (this.$router && !this.isIncident && !this.isNewGroupWorkItem) {
        const routerPushObject = {
          name: ROUTES.workItem,
          params: { iid: workItem.iid },
        };
        if (numberOfDiscussionsResolved) {
          routerPushObject.query = { resolves_discussion: numberOfDiscussionsResolved };
        }
        this.$router.push(routerPushObject);
      } else {
        visitUrl(workItem.webUrl);
      }
    },
    handleCancelClick() {
      const listPath =
        this.$router.history.base + this.$router.history.current.fullPath.replace('/new', '');
      const isWorkItemRoute = this.$route.params?.type === 'work_items';
      const isGroupWorkItemRoute = isWorkItemRoute && this.$router.history.base.includes('groups');

      /**
       * If the route is epics, issues or work items on the group level
       * (because work items on the project level is not yet available)
       * we redirect to the list page when the user clicks on cancel,
       * otherwise we go back to the previous page.
       */
      if (Boolean(listPath) && isWorkItemRoute && isGroupWorkItemRoute) {
        visitUrl(listPath.replaceAll(WORK_ITEM_TYPE_ROUTE_WORK_ITEM, WORK_ITEM_TYPE_ROUTE_ISSUE));
      } else if (Boolean(listPath) && (!isWorkItemRoute || isGroupWorkItemRoute)) {
        visitUrl(listPath);
      } else {
        this.$router.go(-1);
      }
    },
    hideConfirmationModal() {
      this.isCancelConfirmationModalVisible = false;
    },
    showConfirmationModal() {
      this.isCancelConfirmationModalVisible = true;
    },
    handleConfirmCancellation() {
      this.showConfirmationModal();
    },
    handleContinueEditing() {
      this.shouldDiscardDraft = false;
      this.hideConfirmationModal();
    },
    handleDiscardDraft() {
      this.hideConfirmationModal();
      this.handleCancelClick();
      // trigger discard draft function on create work item component
      this.shouldDiscardDraft = true;
    },
  },
};
</script>

<template>
  <div>
    <create-work-item
      :full-path="rootPageFullPath"
      :preselected-work-item-type="workItemType"
      :is-group="isGroup"
      :related-item="relatedItem"
      :should-discard-draft="shouldDiscardDraft"
      :always-show-work-item-type-select="!isEpic"
      :show-project-selector="isNewGroupWorkItem"
      :allowed-work-item-types="allowedWorkItemTypes"
      @updateType="updateWorkItemType($event)"
      @confirmCancel="handleConfirmCancellation"
      @discardDraft="handleDiscardDraft('createPage')"
      @workItemCreated="workItemCreated"
    />
    <create-work-item-cancel-confirmation-modal
      v-if="workItemType"
      :is-visible="isCancelConfirmationModalVisible"
      :work-item-type="workItemType"
      @continueEditing="handleContinueEditing"
      @discardDraft="handleDiscardDraft('confirmModal')"
    />
  </div>
</template>
