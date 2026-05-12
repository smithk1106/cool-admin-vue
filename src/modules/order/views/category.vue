<template>
	<cl-crud ref="Crud">
		<cl-row>
			<!-- 刷新按钮 -->
			<cl-refresh-btn />
			<!-- 新增按钮 -->
			<cl-add-btn />
			<!-- 删除按钮 -->
			<cl-multi-delete-btn />
			<cl-flex1 />
			<!-- 条件搜索 -->
			<cl-search ref="Search" />
		</cl-row>

		<cl-row>
			<!-- 数据表格 -->
			<cl-table ref="Table" />
		</cl-row>

		<!-- 新增、编辑 -->
		<cl-upsert ref="Upsert" />
	</cl-crud>
</template>

<script lang="ts" setup>
	defineOptions({
		name: "order-category"
	});

	import { useCrud, useTable, useUpsert, useSearch } from "@cool-vue/crud";
	import { useCool } from "/@/cool";
	import { useI18n } from "vue-i18n";
	import { Plugins } from "/#/crud";
	import { reactive } from "vue";

	const { service } = useCool();
	const { t } = useI18n();

	// 选项
	const options = reactive({
		isDelete: [
			{ label: t('正常'), value: 0, type: "danger" },
			{ label: t('已删除'), value: 1, type: "success" }
		]
	});

	// cl-upsert
	const Upsert = useUpsert({
		items: [
			{
				label: t('名称'),
				prop: "name",
				component: { name: "el-input", props: { clearable: true } },
				span: 12,
				required: true
			},
			{ label: t('图标'), prop: "icon", component: { name: "cl-upload" } },
			{
				label: t('排序'),
				prop: "sort",
				hook: "number",
				component: { name: "el-input-number", props: { min: 0 } },
				span: 12,
				required: true
			},
			{
				label: t('选择父级'),
				prop: "parentId",
				component: {
					name: "cl-select",
					props: {
						tree: true,
						current: true,
						labelKey: "name",
						valueKey: "id",
						checkStrictly: true
					}
				},
				span: 12
			},
			{
				label: t('已删除'),
				prop: "isDelete",
				component: { name: "el-radio-group", options: options.isDelete },
				value: 0,
				required: true
			}
		]
	});

	// cl-table
	const Table = useTable({
		columns: [
			{ type: "selection" },
			{ label: t('ID'), prop: "id", minWidth: 50, align: "left" },
			{ label: t('名称'), prop: "name", minWidth: 140, align: "left" },
			{
				label: t('图标'),
				prop: "icon",
				minWidth: 100,
				component: { name: "cl-image", props: { size: 60 } }
			},
			{ label: t('排序'), prop: "sort", minWidth: 140, sortable: "custom" },
			{
				label: t('创建时间'),
				prop: "createTime",
				minWidth: 170,
				sortable: "desc",
				component: { name: "cl-date-text" }
			},
			{
				label: t('更新时间'),
				prop: "updateTime",
				minWidth: 170,
				sortable: "custom",
				component: { name: "cl-date-text" }
			},
			{
				type: "op",
				width: 250,
				buttons: [
					"edit",
					"delete"
				]
			}
		],
		plugins: [Plugins.Table.toTree()]
	});

	// cl-search
	const Search = useSearch();

	// cl-crud
	const Crud = useCrud(
		{
			service: service.order.category
		},
		(app) => {
			app.refresh();
		}
	);

	// 刷新
	function refresh(params?: any) {
		Crud.value?.refresh(params);
	}
</script>
