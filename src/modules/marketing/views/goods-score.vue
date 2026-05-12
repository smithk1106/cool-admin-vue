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

	// // 选项
	// const options = reactive({
	// 	status: [
	// 		{ label: t('准备'), value: 0, type: "danger" },
	// 		{ label: t('处理中'), value: 1, type: "warn" },
	// 		{ label: t('订单已发'), value: 2, type: "success" }
	// 	],
	// });

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
				label: t('RANK评分'),
				prop: "rank_score",
				hook: "number",
				component: { name: "el-input-number", props: { min: 0 } },
				span: 12,
				required: true
			},
			{
				label: t('趋势评分'),
				prop: "trend_score",
				hook: "number",
				component: { name: "el-input-number", props: { min: 0 } },
				span: 12,
				required: true
			},
			{
				label: t('价格评分'),
				prop: "price_score",
				hook: "number",
				component: { name: "el-input-number", props: { min: 0 } },
				span: 12,
				required: true
			},
			{
				label: t('点评评分'),
				prop: "review_score",
				hook: "number",
				component: { name: "el-input-number", props: { min: 0 } },
				span: 12,
				required: true
			},
			{
				label: t('评级评分'),
				prop: "rating_score",
				hook: "number",
				component: { name: "el-input-number", props: { min: 0 } },
				span: 12,
				required: true
			},
			{
				label: t('总评分'),
				prop: "total_score",
				hook: "number",
				component: { name: "el-input-number", props: { min: 0 } },
				span: 12,
				required: true
			},
		]
	});

	// cl-table
	const Table = useTable({
		columns: [
			{ type: "selection" },
			{ label: t('名称'), prop: "goodsName", minWidth: 300, align: "left" },
			{ label: t('RANK评分'), prop: "rank_score", minWidth: 100, sortable: "custom" },
			{ label: t('趋势评分'), prop: "trend_score", minWidth: 100, sortable: "custom" },
			{ label: t('价格评分'), prop: "price_score", minWidth: 100, sortable: "custom" },
			{ label: t('点评评分'), prop: "review_score", minWidth: 100, sortable: "custom" },
			{ label: t('评级评分'), prop: "rating_score", minWidth: 100, sortable: "custom" },
			{ label: t('总评分'), prop: "total_score", minWidth: 100, sortable: "custom" },
			// { label: t('状态'), prop: "status", minWidth: 120, dict: options.status },
			{ type: "op", buttons: ["edit", "delete"] }
		]
	});

	// cl-search
	const Search = useSearch();

	// cl-crud
	const Crud = useCrud(
		{
			service: service.marketing.goods_scores
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
