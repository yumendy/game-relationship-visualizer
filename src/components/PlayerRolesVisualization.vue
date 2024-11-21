<template>
  <div ref="chart" style="width: 95vh; height: 95vh;"></div>
</template>

<script>
import * as echarts from 'echarts';

export default {
  name: 'PlayerRolesVisualization',
  props: {
    data: {
      type: Object,
      required: true
    }
  },
  mounted() {
    this.initChart();
  },
  methods: {
    initChart() {
      const chart = echarts.init(this.$refs.chart);
      const option = this.generateOption();
      chart.setOption(option);
    },
    generateOption() {
      const {players, relationships} = this.data;

      // Generate nodes for players and roles
      const nodes = [];
      const links = [];

      const sectColorMap = {
        '少林': '#ffb25f',
        '万花': '#c498ff',
        '天策': '#ff6f53',
        '纯阳': '#16d8d8',
        '七秀': '#ff81b0',
        '五毒': '#3793ff',
        '唐门': '#79b736',
        '藏剑': '#d6f95d',
        '丐帮': '#cd853f',
        '明教': '#f04660',
        '苍云': '#b43c00',
        '长歌': '#64fab4',
        '霸刀': '#6a6cbd',
        '蓬莱': '#abe3fa',
        '凌雪': '#a10922',
        '衍天': '#a653fb',
        '药宗': '#00ac99',
        '刀宗': '#6bb7f2',
        '万灵': '#ebd773',
        '段氏': '#96D5F1',
      };

      players.forEach(player => {
        nodes.push({name: player.name, category: 0, itemStyle: {color: '#eeeeee'}, symbolSize: 30, playInfo: player});
        player.roles.forEach(role => {
          nodes.push({name: role.name, category: 1, itemStyle: {color: sectColorMap[role.sect]}, symbolSize: 15, roleInfo: role});
          links.push({
            source: player.name,
            target: role.name,
            category: 0,
            lineStyle: {color: '#ccc'}
          });
        });
      });

      // Generate links for relationships and player-role associations
      const colorMap = {'亲友': '#5470c6', '师徒': '#91cc75', '情缘': '#e86397'};

      relationships.forEach(rel => {
        links.push({
          source: rel.from,
          target: rel.to,
          relInfo: rel,
          category: 1,
          lineStyle: {color: colorMap[rel.type]}
        });
      });

      return {
        title: {
          text: '原油族谱图',
          top: 'bottom',
          left: 'right'
        },
        series: [
          {
            type: 'graph',
            layout: 'force',
            force: {
              initLayout: 'circular',
              gravity: 0.2,
              repulsion: 600,
              edgeLength: [30,50]
            },
            data: nodes,
            links: links,
            categories: [
              {name: 'Player'},
              {name: 'Role'}
            ],
            roam: true,
            label: {
              show: true,
              position: 'right'
            },
            edgeSymbol: ['none', 'arrow'],
            edgeSymbolSize: [4, 10],
            lineStyle: {
              width: 4,
              curveness: 0.1
            },
            emphasis: {
              focus: 'adjacency',
              lineStyle: {
                width: 6
              }
            }
          }
        ],
        tooltip: {
          formatter: function (params) {
            if (params.dataType === 'node' && params.data.category === 0) {
              let tooltipHtml = `<b>${params.name}</b><br/>${params.data.playInfo.note}`;
              return tooltipHtml
            } else if (params.dataType === 'node' && params.data.category === 1) {
              let roleInfo = params.data.roleInfo;
              let tooltipHtml = `<b>${params.name}</b><br/>${roleInfo.server} ${roleInfo.sect} ${roleInfo.bodyType}</br>${roleInfo.note}`;
              return tooltipHtml
            } else if (params.dataType === 'edge' && params.data.category === 1) {
              let tooltipHtml = `${params.data.relInfo.type}:${params.data.relInfo.note}`;
              return tooltipHtml
            }
            return ''
          }
        },
      }
    },
  }
}
</script>

<style scoped>
/* Add any custom styles here */
</style>