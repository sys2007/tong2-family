<template>
  <div class="main_content">
    <div id="relation"></div>
    <div id="activetime"></div>
  </div>
</template>

<script>
  import echarts from 'echarts'
  import relations from 'static/data/similarityMatrix.json'
  import speaks from 'static/data/dayVectors.json'

  export default {
    data() {
      return {
        names: ['lxx', 'lx', 'pn', 'xrf', 'lxd', 'zrj', 'wsr', 'lcd', 'gt', 'zsx', 'wly', 'yyz', 'zn', 'zls', 'hrh', 'dp', 'yjw', 'ljh', 'ryh', 'zsh', 'mqd', 'prj', 'gyc', 'xzl', 'qy', 'zxz', 'hy', 'hb', 'zly'
          ],
        times: ['00:00', '00:30', '01:00', '01:30', '02:00','02:30','03:00','03:30','04:00','04:30','05:00','05:30','06:00','06:30','07:00','07:30','08:00','08:30','09:00','09:30','10:00','10:30','11:00','11:30','12:00','12:30','13:00','13:30','14:00','14:30','15:00','15:30','16:00','16:30','17:00','17:30','18:00','18:30','19:00','19:30','20:00','20:30','21:00','21:30','22:00','22:30','23:00','23:30']
      }
    },
    methods: {
      convertData (arr) {
        var result = [];
        for (var i = 0; i < arr.length - 1; i++) {
          for (var j = i; j < arr.length-1; j++) {           
            result[result.length] = [i,j,arr[i][j]];
          }
        }

        return result;
      },
      computeTotalSpeak (arr) {
        var totalSpeak = new Array(240);
        for (var i = 0; i < 240; i++) {
          totalSpeak[i] = 0;
          for (var j = 0; j < 30; j++) {
            if (arr[j][i] !== 0) {
              totalSpeak[i] += arr[j][i];
            }        
          }
        }
        var result = new Array(48);
        for (var k = 0; k < 48; k++) {
          result[k] = 0;
          for (var m = 0; m < 5; m++) {
            result[k] += totalSpeak[5*k+m];
          }
        }
        return result;
      },
      drawHeatMap(id) {
        this.chart = echarts.init(document.getElementById(id))
        this.chart.setOption({
          title: {
            text: '通信1502班同学关系密切度分析图（仅通过群聊数据分析）',
            subtext: '数值越大两者越亲密',
            subtextStyle: {
              fontSize: 16
            },
            left: 'center',
            top: 4,
            textStyle: {
              fontSize: 22,
              fontFamily: 'Helvetica',
              fontWeight: 400
            }
          },
          tooltip: {
            trigger: 'item'
          },
          toolbox: {
            feature: {
              saveAsImage: {},
              dataView: {}
            },
            right: 15
          },
          animation: false,
          grid: {
            height: '78%',
            bottom: '14%'
          },
          xAxis: {
            type: 'category',
            data: this.names,
            axisLabel: {
              rotate: 60,
              interval: 0
            },
            splitArea: {
              show: true
            }
          },
          yAxis: {
            type: 'category',
            data: this.names,
            splitArea: {
              show: true
            }  
          },
          visualMap: {
            min: 0,
            max: 100,
            calculable: true,
            itemheight: 300,
            orient: 'horizontal',
            left: 'center',
            bottom: '3%'
          },
          series: [
            {
              name: '亲密度',
              type: 'heatmap',
              data: this.convertData(relations),
              label: {
                normal: {
                  show: true
                }
              },
              itemStyle: {
                emphasis: {
                  shadowBlur: 10,
                  shadowColor: 'rgba(0, 0, 0, 0.5)'
                }
              }
            }
          ]
        })
      },
      drawLine(id) {
        this.chart = echarts.init(document.getElementById(id))
        this.chart.setOption({
          title: {
            text: '通信1502班班群平均每天不同时间段发言量分析',
            left: 'center',
            top: 10,
            textStyle: {
              fontSize: 22,
              fontFamily: 'Helvetica',
              fontWeight: 400
            }
          },
          tooltip: {
            trigger: 'axis'
          },
          toolbox: {
            feature: {
              saveAsImage: {},
              dataView: {}
            },
            left: 16,
            top: 8
          },
          xAxis: {
            data: this.times
          },
          yAxis: {
            splitLine: {
                show: false
            }
          },
          dataZoom: [{
            startValue: '00：00'
          }, {
            type: 'inside'
          }],
          visualMap: {
            top: 10,
            right: 10,
            pieces: [{
              gt: 0,
              lte: 100,
              color: '#096'
            }, {
              gt: 100,
              lte: 200,
              color: '#ffde33'
            }, {
              gt: 200,
              lte: 300,
              color: '#ff9933'
            }, {
              gt: 300,
              lte: 400,
              color: '#cc0033'
            }, {
              gt: 400,
              lte: 600,
              color: '#660099'
            }, {
              gt: 600,
              color: '#7e0023'
            }],
            outOfRange: {
              color: '#999'
            }
          },
          series: {
            name: '发言量',
            type: 'line',
            symbol: 'triangle',
            symbolSize: 8,
            data: this.computeTotalSpeak(speaks),
            markLine: {
              silent: true,
              data: [{
                yAxis: 100
              }, {
                yAxis: 200
              }, {
                yAxis: 300
              }, {
                yAxis: 400
              }, {
                yAxis: 600
              }]
            }
          }
        })
      }
    },
    mounted() {
      // console.log(this.computeTotalSpeak(speaks))
      // console.log(speaks)
      // console.log(this.convertData(relations))
      this.$nextTick(function() {
        this.drawHeatMap('relation')
        this.drawLine('activetime')
      })
    }
  }
</script>

<style scoped>
.main_content {
  position: relative;
  margin-left: 245px;
  margin-top: 100px;
}
#relation,
#activetime {
  position: relative;
  left: 50%;
  margin-left: -400px;
  width: 800px;
  height: 900px;
  box-shadow: 0 0 10px #EDE68A;
  border-radius: 10px;
} 
#activetime {
  height: 600px;
  margin-top: 30px;
  margin-bottom: 70px;
}
@media screen and (max-width: 1090px) {
  #relation,
  #activetime {
    position: absolute;
    left: 415px;
  }
  #activetime {
    top: 900px;
  }
}
</style>