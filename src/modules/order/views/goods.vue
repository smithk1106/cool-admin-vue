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

		<cl-row>
			<cl-flex1 />
			<!-- 分页控件 -->
			<cl-pagination />
		</cl-row>

		<!-- 新增、编辑 -->
		<cl-upsert ref="Upsert" />
	</cl-crud>
</template>

<script lang="ts" setup>
	defineOptions({
		name: "order-goods"
	});

	import { useCrud, useTable, useUpsert, useSearch } from "@cool-vue/crud";
	import { useCool } from "/@/cool";
	import { useI18n } from "vue-i18n";
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
			{ label: t('图片'), prop: "pic", component: { name: "cl-upload" } },
			{
				label: t('名称'),
				prop: "name",
				component: { name: "el-input", props: { clearable: true } },
				span: 12,
				required: true
			},
			{
				label: t('SKU'),
				prop: "sku",
				component: { name: "el-input", props: { clearable: true } },
				span: 12
			},
			{
				label: t('价格'),
				prop: "price",
				hook: "number",
				component: { name: "el-input-number", props: { min: 0 } },
				span: 12,
				required: true
			},
			{
				label: t('库存'),
				prop: "stock",
				hook: "number",
				component: { name: "el-input-number", props: { min: 0 } },
				span: 12,
				required: true
			},
			{
				label: t('排序'),
				prop: "sort",
				hook: "number",
				component: { name: "el-input-number", props: { min: 0 } },
				span: 12,
				required: true
			},
			{
				label: t('选择分类'),
				prop: "categoryId",
				component: {
					name: "cl-select",
					props: {
						tree: true,
						labelKey: "name",
						valueKey: "id",
						checkStrictly: true,
						api: () => service.order.category.list()
					}
				},
				span: 12
			},
			// {
			// 	label: t('删除标记'),
			// 	prop: "isDelete",
			// 	component: { name: "el-radio-group", options: options.isDelete },
			// 	value: 0,
			// 	required: true
			// }
		]
	});

	// cl-table
	const Table = useTable({
		columns: [
			{ type: "selection" },
			{ label: t('ID'), prop: "id", minWidth: 60, align: "left" },
			{ label: t('分类名'), prop: "categoryName", minWidth: 140 },
			{
				label: t('图片'),
				prop: "pic",
				minWidth: 100,
				component: { name: "cl-image", props: { size: 60 } }
			},
			{ label: t('名称'), prop: "name", minWidth: 140 },
			{ label: t('SKU'), prop: "sku", minWidth: 140 },
			{ label: t('规格'), prop: "specName", minWidth: 140 },
			{ label: t('价格'), prop: "price", minWidth: 140, sortable: "custom" },
			{ label: t('库存'), prop: "stock", minWidth: 140, sortable: "custom" },
			{ label: t('排序'), prop: "sort", minWidth: 140, sortable: "custom" },
			// {
			// 	label: t('创建时间'),
			// 	prop: "createTime",
			// 	minWidth: 170,
			// 	sortable: "desc",
			// 	component: { name: "cl-date-text" }
			// },
			{
				label: t('更新时间'),
				prop: "updateTime",
				minWidth: 170,
				sortable: "custom",
				component: { name: "cl-date-text" }
			},
            // {
			// 	label: t('删除'),
			// 	prop: "isDelete",
			// 	minWidth: 100,
			// 	component: { name: "cl-switch" },
			// 	dict: options.isDelete
			// },
			{ type: "op", buttons: ["edit", "delete"] }
		]
	});

	// cl-search
	const Search = useSearch();

	// cl-crud
	const Crud = useCrud(
		{
			service: service.order.goods
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
