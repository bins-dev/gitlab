<script>
import { GlDisclosureDropdown, GlTooltipDirective } from '@gitlab/ui';
import { identity, uniqueId } from 'lodash';
import { __ } from '~/locale';

export default {
  name: 'GlqlActions',
  components: {
    GlDisclosureDropdown,
  },
  directives: {
    GlTooltip: GlTooltipDirective,
  },
  props: {
    modalTitle: {
      type: String,
      required: false,
      default: '',
    },
    showCopyContents: {
      type: Boolean,
      required: false,
      default: false,
    },
  },
  data() {
    return {
      toggleId: uniqueId('dropdown-toggle-btn-'),
    };
  },
  computed: {
    items() {
      return [
        {
          text: __('View source'),
          action: () => this.$emit('viewSource', { title: this.modalTitle }),
        },
        {
          text: __('Copy source'),
          action: () => this.$emit('copySource'),
        },
        this.showCopyContents && {
          text: __('Copy contents'),
          action: () => this.$emit('copyAsGFM'),
        },
        {
          text: __('Reload'),
          action: () => this.$emit('reload'),
        },
      ].filter(identity);
    },
  },
};
</script>
<template>
  <div class="gl-inline-flex gl-self-start gl-align-middle">
    <gl-disclosure-dropdown
      v-gl-tooltip
      class="glql-actions"
      :title="__('Embedded view options')"
      :items="items"
      :toggle-id="toggleId"
      :no-caret="true"
      size="small"
      category="tertiary"
      icon="ellipsis_v"
      :toggle-text="__('Embedded view options')"
      text-sr-only
      placement="bottom-end"
    />
  </div>
</template>
