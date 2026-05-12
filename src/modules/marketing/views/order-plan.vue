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
		name: "order-plan"
	});

	import { useCrud, useTable, useUpsert, useSearch } from "@cool-vue/crud";
	import { useCool } from "/@/cool";
	import { useI18n } from "vue-i18n";
	import { reactive } from "vue";
	import GoodsSelect from "/$/order/components/goods-select.vue";

	const { service } = useCool();
	const { t } = useI18n();

	// 选项
	const options = reactive({
		status: [
			{ label: t('准备'), value: 0, type: "info" },
			{ label: t('处理中'), value: 1, type: "warning" },
			{ label: t('订单已发'), value: 2, type: "success" }
		],
	});

	// cl-upsert
	const Upsert = useUpsert({
		items: [
			{
				label: t('选择商品'),
				prop: "goodsId",
				component: { vm: GoodsSelect },
				span: 12,
				required: true
			},
			{
				label: t('价格'),
				prop: "inPrice",
				hook: "number",
				component: { name: "el-input-number", props: { min: 0 } },
				span: 12,
				required: true
			},
			{
				label: t('进货数量'),
				prop: "inCount",
				hook: "number",
				component: { name: "el-input-number", props: { min: 0 } },
				span: 12,
				required: true
			},
			{
				label: t('状态'),
				prop: "status",
				component: { name: "el-radio-group", options: options.status },
				value: 0,
				required: true
			}
		]
	});

	// cl-table
	const Table = useTable({
		columns: [
			{ type: "selection" },
			{ label: t('名称'), prop: "goodsName", minWidth: 300, align: "left" },
			{ label: t('价格'), prop: "inPrice", minWidth: 100, sortable: "custom" },
			{ label: t('进货数量'), prop: "inCount", minWidth: 100, sortable: "custom" },
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
			{ label: t('状态'), prop: "status", minWidth: 120, dict: options.status },
			{ type: "op", buttons: ["edit", "delete"] }
		]
	});

	// cl-search
	const Search = useSearch();

	// cl-crud
	const Crud = useCrud(
		{
			service: service.marketing.goods_order_plan
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
