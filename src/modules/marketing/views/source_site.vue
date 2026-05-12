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
						v-model="scope.name"
						:placeholder="$t('请填写名称')"
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
	name: 'source-site'
});

import { useCrud, useTable, useUpsert } from '@cool-vue/crud';
import { useCool } from '/@/cool';
import { useI18n } from 'vue-i18n';
import { Plugins } from '/#/crud';
import { onMounted, reactive } from 'vue';

const { service } = useCool();
const { t } = useI18n();

const options = reactive({
	categories: [
		{ label: t('全部'), value: 0, type: "info" }
	]
})

onMounted(() => {
  // コンポーネントがマウントされた後に実行される処理
  service.order.category.list().then((res: any) => {
	for (const item of res) {
		options.categories.push({
			label: item.name,
			value: item.id,
			type: "success"
		})
	}
  })
});

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
			label: t('选择分类'),
			prop: "categoryId",
			component: {
				name: "cl-select",
				props: {
					tree: false,
					labelKey: "label",
					valueKey: "value",
					checkStrictly: true,
					options: options.categories
				}
			},
			span: 12
		},
		{
			label: t('排序'),
			prop: 'sort',
			value: 1,
			component: { name: 'el-input-number', props: { min: 1 } }
		},
		{
			label: t('抓取URL'),
			prop: 'url',
			component: {
				name: 'el-input',
				props: { type: 'textarea', rows: 4 }
			}
		}
	],
	onSubmit(data, { next }) {
		next({
			...data,
			typeId: 21
		});
	},
	plugins: [Plugins.Form.setFocus('name')]
});

// cl-table
const Table = useTable({
	columns: [
		{
			type: 'selection'
		},
		{ label: t('名称'), prop: 'name', align: 'left', minWidth: 200 },
		{
			label: t('对应分类'),
			prop: 'categoryId',
			align: 'left',
			minWidth: 150,
			dict: options.categories,
			//showOverflowTooltip: true
		},
		{
			label: t('抓取URL'),
			prop: 'url',
			showOverflowTooltip: true,
			minWidth: 300,
			align: "left"
		},
		{
			label: t('排序'),
			prop: 'sort',
			sortable: 'desc',
			width: 100,
			// fixed: 'right'
		},
		{ label: t('ID'), prop: 'id', minWidth: 50 },
		{
			type: 'op',
			width: 250,
			buttons: [
				{
					label: t('新增'),
					type: 'success',
					//hidden: !service.marketing.source_site.info._permission.add,
					onClick({ scope }) {
						append(scope.row);
					}
				},
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
		service: service.marketing.source_site
	},
	(app) => {
		app.refresh({
			prop: 'sort',
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
