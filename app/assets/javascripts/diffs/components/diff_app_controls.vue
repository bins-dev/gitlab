<script>
import { GlButton, GlButtonGroup, GlTooltipDirective } from '@gitlab/ui';
import SettingsDropdown from '~/diffs/components/settings_dropdown.vue';
import DiffStats from '~/diffs/components/diff_stats.vue';
import { sanitize } from '~/lib/dompurify';
import {
  keysFor,
  MR_COLLAPSE_ALL_FILES,
  MR_EXPAND_ALL_FILES,
} from '~/behaviors/shortcuts/keybindings';
import { Mousetrap } from '~/lib/mousetrap';

const createHotkeyHtml = (key) => `<kbd class="flat gl-ml-1" aria-hidden=true>${key}</kbd>`;

export default {
  name: 'DiffAppControls',
  components: { DiffStats, SettingsDropdown, GlButton, GlButtonGroup },
  directives: {
    glTooltip: GlTooltipDirective,
  },
  props: {
    hasChanges: {
      type: Boolean,
      required: true,
    },
    diffsCount: {
      type: [String, Number],
      default: '',
      required: false,
    },
    addedLines: {
      type: Number,
      required: false,
      default: null,
    },
    removedLines: {
      type: Number,
      required: false,
      default: null,
    },
    showWhitespace: {
      type: Boolean,
      required: true,
    },
    diffViewType: {
      type: String,
      required: true,
    },
    viewDiffsFileByFile: {
      type: Boolean,
      required: true,
    },
    isLoading: {
      type: Boolean,
      required: false,
      default: false,
    },
    fileByFileSupported: {
      type: Boolean,
      required: false,
      default: true,
    },
    hideOnNarrowScreen: {
      type: Boolean,
      required: false,
      default: true,
    },
  },
  computed: {
    expandButtonInfo() {
      const keys = keysFor(MR_EXPAND_ALL_FILES);
      return {
        title: MR_EXPAND_ALL_FILES.description,
        keys,
        tooltip: sanitize(`${MR_EXPAND_ALL_FILES.description} ${createHotkeyHtml(keys[0])}`),
      };
    },
    collapseButtonInfo() {
      const keys = keysFor(MR_COLLAPSE_ALL_FILES);
      return {
        title: MR_COLLAPSE_ALL_FILES.description,
        keys,
        tooltip: sanitize(`${MR_COLLAPSE_ALL_FILES.description} ${createHotkeyHtml(keys[0])}`),
      };
    },
  },
  mounted() {
    Mousetrap.bind(this.expandButtonInfo.keys, this.expandAllFiles);
    Mousetrap.bind(this.collapseButtonInfo.keys, this.collapseAllFiles);
  },
  beforeDestroy() {
    Mousetrap.unbind(this.expandButtonInfo.keys, this.expandAllFiles);
    Mousetrap.unbind(this.collapseButtonInfo.keys, this.collapseAllFiles);
  },
  methods: {
    expandAllFiles() {
      if (this.isLoading) return;
      this.$emit('expandAllFiles');
    },
    collapseAllFiles() {
      if (this.isLoading) return;
      this.$emit('collapseAllFiles');
    },
  },
};
</script>

<template>
  <div class="gl-items-center" :class="hideOnNarrowScreen ? 'gl-hidden md:gl-flex' : 'gl-flex'">
    <template v-if="hasChanges">
      <diff-stats
        v-if="diffsCount !== ''"
        class="inline-parallel-buttons ml-auto"
        :diffs-count="diffsCount"
        :added-lines="addedLines"
        :removed-lines="removedLines"
        :hide-on-narrow-screen="hideOnNarrowScreen"
      />
      <gl-button-group class="gl-mr-3">
        <gl-button
          v-gl-tooltip.html="expandButtonInfo.tooltip"
          icon="expand"
          variant="default"
          :aria-label="expandButtonInfo.title"
          :aria-keyshortcuts="expandButtonInfo.keys[0]"
          :disabled="isLoading"
          @click="$emit('expandAllFiles')"
        />
        <gl-button
          v-gl-tooltip.html="collapseButtonInfo.tooltip"
          icon="collapse"
          variant="default"
          :aria-label="collapseButtonInfo.title"
          :aria-keyshortcuts="collapseButtonInfo.keys[0]"
          :disabled="isLoading"
          @click="$emit('collapseAllFiles')"
        />
      </gl-button-group>
    </template>
    <settings-dropdown
      :show-whitespace="showWhitespace"
      :view-diffs-file-by-file="viewDiffsFileByFile"
      :diff-view-type="diffViewType"
      :file-by-file-supported="fileByFileSupported"
      @updateDiffViewType="$emit('updateDiffViewType', $event)"
      @toggleWhitespace="$emit('toggleWhitespace', $event)"
      @toggleFileByFile="$emit('toggleFileByFile', $event)"
    />
  </div>
</template>
