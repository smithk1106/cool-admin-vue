<template>
	<cl-crud ref="Crud">
		<cl-row>
			<!-- 刷新按钮 -->
			<cl-refresh-btn />
			<!-- 新增按钮 -->
			<cl-add-btn />
			<!-- 批量删除 -->
			<cl-multi-delete-btn />
			<cl-flex1 />
		</cl-row>

		<cl-row>
			<!-- 数据表格 -->
			<cl-table ref="Table" />
		</cl-row>

		<cl-row>
			<cl-flex1 />
		</cl-row>

		<!-- 新增、编辑 -->
		<cl-upsert ref="Upsert">
			<template #slot-value="{ scope }">
				<div>
					<el-input
						v-model="scope.value"
						:placeholder="$t('请填写值')"
						clearable
						type="textarea"
						:rows="4"
						class="mb-2"
					/>
				</div>
			</template>
		</cl-upsert>
	</cl-crud>
</template>

<script lang="ts" setup>
defineOptions({
	name: 'dict-list'
});

import { useCrud, useTable, useUpsert } from '@cool-vue/crud';
import { useCool } from '/@/cool';
import { useDict } from '../index';
import { useI18n } from 'vue-i18n';
import { Plugins } from '/#/crud';

const { service } = useCool();
const { dict } = useDict();
const { t } = useI18n();

// cl-upsert
const Upsert = useUpsert({
	dialog: {
		width: '600px'
	},
	props: {
		labelWidth: '100px'
	},
	items: [
		{
			label: t('上级节点'),
			prop: 'parentId',
			component: {
				name: 'cl-select',
				props: {
					labelKey: 'name',
					valueKey: 'id',
					checkStrictly: true,
					tree: true,
					current: true,
					defaultExpandAll: true
				}
			}
		},
		{
			label: t('名称'),
			prop: 'name',
			required: true,
			component: { name: 'el-input' }
		},
		{
			label: t('值'),
			prop: 'value',
			component: { name: 'slot-value' }
		},
		{
			label: t('排序'),
			prop: 'orderNum',
			value: 1,
			component: { name: 'el-input-number', props: { min: 1 } }
		},
		{
			label: t('备注'),
			prop: 'remark',
			component: {
				name: 'el-input',
				props: { type: 'textarea', rows: 4 }
			}
		}
	],
	onSubmit(data, { next }) {
		next({
			...data,
			typeId: 20
		});
	},
	plugins: [Plugins.Form.setFocus('name')]
});

// cl-table
const Table = useTable({
	contextMenu: [
		'refresh',
		row => {
			return {
				label: t('新增'),
				hidden: !service.dict.info._permission?.add,
				callback(done) {
					append(row);
					done();
				}
			};
		},
		'edit',
		'delete',
		'order-asc',
		'order-desc'
	],
	columns: [
		{
			type: 'selection'
		},
		// { label: t('ID'), prop: 'id', minWidth: 120 },
		{ label: t('名称'), prop: 'name', align: 'left', minWidth: 150 },
		{
			label: t('值'),
			prop: 'value',
			align: 'left',
			minWidth: 300,
			showOverflowTooltip: true
		},
		{
			label: t('备注'),
			prop: 'remark',
			showOverflowTooltip: true,
			minWidth: 200
		},
		{
			label: t('排序'),
			prop: 'orderNum',
			sortable: 'desc',
			width: 100,
			fixed: 'right'
		},
		{
			type: 'op',
			width: 200,
			buttons: [
				'edit',
				'delete'
			]
		}
	],
	plugins: [Plugins.Table.toTree()]
});

// cl-crud
const Crud = useCrud(
	{
		service: service.dict.info
	},
	(app) => {
		app.refresh({
			typeId: 20,
			page: 1,
			prop: 'orderNum',
			order: 'desc'
		});
	}
);

// 刷新
function refresh(params?: any) {
	Crud.value?.refresh(params);
}

// 追加子集
function append(row: any) {
	Crud.value?.rowAppend({
		parentId: row.id,
		orderNum: 1
	});
}
</script>
