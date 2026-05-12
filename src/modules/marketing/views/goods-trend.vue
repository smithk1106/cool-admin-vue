<template>
	<div class="trend">
		<el-row :gutter="10">
			<el-col :lg="24" :xs="24">
				<tab-chart />
			</el-col>
		</el-row>

		<el-row :gutter="1">
			<el-col :lg="24" :xs="24">
				<cl-crud ref="Crud">
					<cl-row>
						<!-- 数据表格 -->
						<cl-table ref="Table" />
					</cl-row>

					<cl-row>
						<cl-flex1 />
						<!-- 分页控件 -->
						<cl-pagination />
					</cl-row>
				</cl-crud>
			</el-col>
		</el-row>
	</div>
</template>

<script lang="ts" setup>
	defineOptions({
		name: "goods-trend"
	});

	import TabChart from '../components/tab-chart.vue';
	import { useCrud, useTable, useUpsert, useSearch } from "@cool-vue/crud";
	import { useCool } from "/@/cool";
	import { useI18n } from "vue-i18n";
	import { reactive } from "vue";

	const { service } = useCool();
	const { t } = useI18n();

	// 选项
	const options = reactive({
		comparePrev: [
			{ label: t('准备'), value: 0, type: "danger" },
			{ label: t('订单已发'), value: 2, type: "success" }
		],
	});

	// cl-table
	const Table = useTable({
		autoHeight: false,
		height: 200,
		columns: [
			{ type: "selection" },
			{ label: t('Rank'), prop: "rank", minWidth: 50 },
			{ label: t('名称'), prop: "goodsName", minWidth: 200, align: "left" },
			// { label: t('价格'), prop: "price", minWidth: 100, sortable: "custom" },
			{ label: t('人气度'), prop: "score", minWidth: 100, sortable: "custom" },
			{ label: t('前日比'), prop: "dailyDiff", minWidth: 100, sortable: "custom", formatter(row, column, value, index) {
				return value > 10 ? "↑ "+value : "↓ "+value
			}, },
			// {
			// 	label: t('创建时间'),
			// 	prop: "createTime",
			// 	minWidth: 170,
			// 	sortable: "desc",
			// 	component: { name: "cl-date-text" }
			// },
			// {
			// 	label: t('更新时间'),
			// 	prop: "updateTime",
			// 	minWidth: 170,
			// 	sortable: "custom",
			// 	component: { name: "cl-date-text" }
			// }
		]
	});

	// cl-search
	const Search = useSearch();

	// cl-crud
	const Crud = useCrud(
		{
			service: service.marketing.goods_history
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
