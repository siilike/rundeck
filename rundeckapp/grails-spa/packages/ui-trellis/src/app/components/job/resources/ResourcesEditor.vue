<template>
  <div v-if="modelData">
    <div class="form-group">
        <div class="col-sm-2 " style="text-align: right">
            <label class="control-label">
                <ui-socket section="resources-editor" location="section-title">
                {{ $t('resourcesEditor.Nodes') }}
                </ui-socket>
            </label>
            <ui-socket section="resources-editor" location="section-title-help"/>
        </div>

      <div class="col-sm-10 ">
        <div class="radio radio-inline">
          <input type="radio"
                 name="doNodedispatch"
                 :value="true"
                 class="xnode_dispatch_radio"
                 v-model="modelData.doNodedispatch"
                 id="doNodedispatchTrue"/>
          <label for="doNodedispatchTrue">
              <ui-socket section="resources-editor" location="node-dispatch-true-label">
              {{ $t('resourcesEditor.Dispatch to Nodes') }}
              </ui-socket>
          </label>
        </div>
        <div class="radio radio-inline">
          <input id="doNodedispatchFalse"
                 type="radio"
                 name="doNodedispatch"
                 :value="false"
                 v-model="modelData.doNodedispatch"
                 class="xnode_dispatch_radio"/>
          <label for="doNodedispatchFalse">
              <ui-socket section="resources-editor" location="node-dispatch-false-label">
              {{ $t('execute.locally') }}
              </ui-socket>
          </label>
        </div>
      </div>
    </div>

    <template v-if="modelData.doNodedispatch">


      <div class="form-group  " :class="{'has-error':modelData.filterErrors  && modelData.filterErrors.length>0}">
        <div class="subfields nodeFilterFields ">
          <label class="col-sm-2 control-label">
            {{ $t("node.filter") }}
          </label>

          <div class="col-sm-10">

            <inline-validation-errors :errors="modelData.filterErrors"/>

            <node-filter-input
                id="job_edit__node_filter_include"
                v-model="modelData.filter"
                               :filter-name="modelData.filterName"
                               :node-summary="nodeSummary"
                               :allow-filter-default="false"
                               @filters-updated="loadNodeSummary"
                               @filter="handleFilterClick"/>

            <input type="hidden" name="project" :value="modelData.project"/>


          </div>


        </div>
      </div>
      <div class="form-group  "
           :class="{'has-error':modelData.filterExcludeErrors  && modelData.filterExcludeErrors.length>0}"
           v-if="modelData.doNodedispatch">
        <div class="subfields nodeFilterFields ">
          <label class="col-sm-2 control-label">
            {{ $t("node.filter.exclude") }}
          </label>

          <div class="col-sm-10">

            <inline-validation-errors :errors="modelData.filterExcludeErrors"/>

            <node-filter-input
                id="job_edit__node_filter_exclude"
                v-model="modelData.filterExclude"
                               :filter-name="modelData.filterNameExclude"
                               :node-summary="nodeSummary"
                               :allow-filter-default="false"
                               filter-field-name="filterExclude"
                               filter-field-id="schedJobNodeFilterExclude"
                               :help-button="false"
                               search-btn-type="default"
                               @filters-updated="loadNodeSummary"
                               @filter="handleFilterExcludeClick"/>

          </div>


        </div>
      </div>

      <div class="form-group">
        <div class="col-sm-2 control-label text-form-label">
          {{ $t('scheduledExecution.property.excludeFilterUncheck.label') }}
        </div>

        <div class="col-sm-10">
          <div class="radio radio-inline">
            <input type="radio"
                   name="excludeFilterUncheck"
                   :value="true"
                   v-model="modelData.excludeFilterUncheck"
                   id="excludeFilterTrue"/>
            <label for="excludeFilterTrue">
              {{ $t('yes') }}
            </label>
          </div>

          <div class="radio radio-inline">
            <input type=radio
                   :value="false"
                   name="excludeFilterUncheck"
                   v-model="modelData.excludeFilterUncheck"
                   id="excludeFilterFalse"/>
            <label for="excludeFilterFalse">
              {{ $t('no') }}
            </label>
          </div>

          <span class="help-block">
                {{ $t('scheduledExecution.property.excludeFilterUncheck.description') }}
          </span>
        </div>
      </div>

      <input type="hidden" name="nodeExcludePrecedence" value="true"/>

      <div class="form-group">
        <label :class="labelColClass">
          {{ $t('matched.nodes.prompt') }}
        </label>

        <div class="   container-fluid" :class="fieldColSize">
          <node-filter-results :node-filter="modelData.filter"
                               :node-exclude-filter="modelData.filterExclude"
                               :exclude-filter-uncheck="modelData.excludeFilterUncheck"
                               @filter="handleFilterClick"/>
        </div>
      </div>

      <div class="form-group">
        <div class="col-sm-2 control-label text-form-label">
          {{ $t('scheduledExecution.property.nodefiltereditable.label') }}
        </div>

        <div class="col-sm-10">
          <div class="radio radio-inline">
            <input type="radio"
                   :value="false"
                   name="nodeFilterEditable"
                   v-model="modelData.nodeFilterEditable"
                   id="editableFalse"/>
            <label for="editableFalse">
              {{ $t('no') }}
            </label>
          </div>
          <div class="radio radio-inline">
            <input type="radio"
                   name="nodeFilterEditable"
                   :value="true"
                   v-model="modelData.nodeFilterEditable"
                   id="editableTrue"/>
            <label for="editableTrue">
              {{ $t('yes') }}
            </label>
          </div>
        </div>
      </div>

      <div class="form-group "
           :class="{'has-errors':modelData.filterExcludeErrors  && modelData.filterExcludeErrors.length>0}">
        <label for="schedJobnodeThreadcount" :class="labelColClass">
          {{ $t('scheduledExecution.property.nodeThreadcount.label') }}
        </label>

        <div :class="fieldColSize">
          <div class="row">
            <div class="col-sm-4">
              <input type='text'
                     name="nodeThreadcountDynamic"
                     v-model="modelData.nodeThreadcountDynamic"
                     id="schedJobnodeThreadcount"
                     size="3"
                     class="form-control input-sm"/>
            </div>
          </div>
          <inline-validation-errors :errors="modelData.nodeThreadcountDynamicErrors"/>
          <span class="help-block">
          {{ $t('scheduledExecution.property.nodeThreadcount.description') }}
        </span>

        </div>
      </div>

      <div class="form-group "
           :class="{'has-errors':modelData.nodeRankAttributeErrors  && modelData.nodeRankAttributeErrors.length>0}">
        <label for="schedJobnodeRankAttribute" :class="labelColClass">
          {{ $t('scheduledExecution.property.nodeRankAttribute.label') }}
        </label>

        <div :class="fieldColSize">
          <div class="row">
            <div class="col-sm-4">
              <input type='text'
                     name="nodeRankAttribute"
                     v-model="modelData.nodeRankAttribute"
                     id="schedJobnodeRankAttribute"
                     class="form-control input-sm"/>
            </div>
          </div>
          <inline-validation-errors :errors="modelData.nodeRankAttributeErrors"/>
          <span class="help-block">
          {{ $t('scheduledExecution.property.nodeRankAttribute.description') }}
        </span>
        </div>
      </div>


      <div class="form-group">
        <label :class="labelColClass">
          {{ $t('scheduledExecution.property.nodeRankOrder.label') }}
        </label>

        <div :class="fieldColSize">
          <div class="radio radio-inline">
            <input type="radio"
                   name="nodeRankOrderAscending"
                   :value="true"
                   v-model="modelData.nodeRankOrderAscending"
                   id="nodeRankOrderAscending"/>
            <label for="nodeRankOrderAscending">
              {{ $t('scheduledExecution.property.nodeRankOrder.ascending.label') }}
            </label>
          </div>
          <div class="radio radio-inline">
            <input type="radio"
                   name="nodeRankOrderAscending"
                   :value="false"
                   v-model="modelData.nodeRankOrderAscending"
                   id="nodeRankOrderDescending"/>
            <label for="nodeRankOrderDescending">
              {{ $t('scheduledExecution.property.nodeRankOrder.descending.label') }}
            </label>
          </div>
        </div>
      </div>

      <div class="form-group">
        <label :class="labelColClass">{{ $t('scheduledExecution.property.nodeKeepgoing.prompt') }}</label>

        <div :class="fieldColSize">
          <div class="radio">
            <input type="radio"
                   name="nodeKeepgoing"
                   :value="false"
                   v-model="modelData.nodeKeepgoing"
                   id="nodeKeepgoingFalse"/>
            <label for="nodeKeepgoingFalse">
              {{ $t('scheduledExecution.property.nodeKeepgoing.false.description') }}
            </label>
          </div>

          <div class="radio">
            <input type="radio"
                   name="nodeKeepgoing"
                   :value="true"
                   v-model="modelData.nodeKeepgoing"
                   id="nodeKeepgoingTrue"/>
            <label for="nodeKeepgoingTrue">
              {{ $t('scheduledExecution.property.nodeKeepgoing.true.description') }}
            </label>
          </div>
        </div>
      </div>
      <div class="form-group">
        <label :class="labelColClass">{{ $t('scheduledExecution.property.successOnEmptyNodeFilter.prompt') }}</label>

        <div :class="fieldColSize">
          <div class="radio">
            <input type="radio" name="successOnEmptyNodeFilter"
                   :value="false"
                   v-model="modelData.successOnEmptyNodeFilter"
                   id="successOnEmptyNodeFilterFalse"/>
            <label for="successOnEmptyNodeFilterFalse">
              {{ $t('scheduledExecution.property.successOnEmptyNodeFilter.false.description') }}
            </label>
          </div>
          <div class="radio">
            <input type="radio"
                   name="successOnEmptyNodeFilter"
                   :value="true"
                   v-model="modelData.successOnEmptyNodeFilter"
                   id="successOnEmptyNodeFilterTrue"/>
            <label for="successOnEmptyNodeFilterTrue">
              {{ $t('scheduledExecution.property.successOnEmptyNodeFilter.true.description') }}
            </label>
          </div>
        </div>
      </div>
      <div class="form-group">
        <label :class="labelColClass">{{ $t('scheduledExecution.property.nodesSelectedByDefault.label') }}</label>

        <div :class="fieldColSize">
          <div class="radio">
            <input type="radio"
                   name="nodesSelectedByDefault"
                   :value="true"
                   v-model="modelData.nodesSelectedByDefault"
                   id="nodesSelectedByDefaultTrue"/>
            <label for="nodesSelectedByDefaultTrue">
              {{ $t('scheduledExecution.property.nodesSelectedByDefault.true.description') }}
            </label>
          </div>
          <div class="radio">
            <input type="radio" name="nodesSelectedByDefault"
                   :value="false"
                   v-model="modelData.nodesSelectedByDefault"
                   id="nodesSelectedByDefaultFalse"/>
            <label for="nodesSelectedByDefaultFalse">
              {{ $t('scheduledExecution.property.nodesSelectedByDefault.false.description') }}
            </label>
          </div>
        </div>
      </div>
      <orchestrator-editor :label-col-class="labelColClass" :field-col-size="fieldColSize"
                           v-model="modelData.orchestrator"/>

    </template>
  </div>
</template>
<script lang="ts">
import OrchestratorEditor from '../../../components/job/resources/OrchestratorEditor.vue'
import {_genUrl} from '../../../utilities/genUrl'
import axios from 'axios'
import InlineValidationErrors from '../../form/InlineValidationErrors.vue'
import { defineComponent, ref } from 'vue'
import type { PropType } from "vue"
import NodeFilterInput from './NodeFilterInput.vue'
import NodeFilterResults from './NodeFilterResults.vue'

import {
  getAppLinks
} from '../../../../library'
import UiSocket from "../../../../library/components/utils/UiSocket.vue";

export default defineComponent({
  name: 'ResourcesEditor',
  components: {
    OrchestratorEditor,
    InlineValidationErrors,
    NodeFilterInput,
    NodeFilterResults,
    UiSocket,
  },
  props: {
    modelValue: {
      type: Object as PropType<any>,
      required: true,
    },
    eventBus: {
      type: Object as PropType<any>,
      required: true,
    },
  },
  emits: ['update:modelValue'],
  computed: {
    labelColClass() {
      return 'col-sm-2 control-label'
    },
    fieldColSize() {
      return 'col-sm-10'
    }
  },
  setup() {
    const modelData = ref<any>({doNodedispatch: false, orchestrator: {type: null, config: {}}})
    const nodeSummary = ref<any>({})
    return {
      modelData,
      nodeSummary,
    }
  },
  methods: {
    async loadNodeSummary() {
      let result = await axios.get(_genUrl(getAppLinks().frameworkNodeSummaryAjax, {}))
      if (result.status >= 200 && result.status < 300) {
        this.nodeSummary = result.data
      }
    },
    handleFilterExcludeClick(val: any) {
      this.handleFilterClick({filterExclude: val.filter, filterNameExclude: val.filterName})
    },
    handleFilterClick(val: any) {
      if (val.filter) {
        this.modelData.filter = val.filter
      }
      if (val.filterName) {
        this.modelData.filterName = val.filterName
      }
      if (val.filterExclude) {
        this.modelData.filterExclude = val.filterExclude
      }
      if (val.filterNameExclude) {
        this.modelData.filterNameExclude = val.filterNameExclude
      }
    },
    async onMount() {
      this.modelData = {
        ...this.modelValue,
        filter: this.modelValue.filter || '',
        filterExclude: this.modelValue.filterExclude || '',
      }
      await this.loadNodeSummary()
    }
  },
  mounted() {
    this.onMount()
  },
  watch: {
    modelData: {
      handler() {
        this.$emit('update:modelValue', this.modelData)
      },
      deep: true,
    }
  },
})
</script>
