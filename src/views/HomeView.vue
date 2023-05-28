<script>
import GraphComponent from '../components/GraphComponent.vue';
export default {
  name: 'Home',
  components: {
    GraphComponent,
  },
  data() {
    return {
      jsonData: [],
      nameFilter: '',
      startYear: 0,
      endYear: 0,
      years: [2550, 2551, 2552, 2553, 2554, 2555, 2556, 2557, 2558, 2559],
      filteredData: [],
    }
  },
  mounted() {
    fetch('/bkk_population_growth.csv')
      .then(response => response.text())
      .then(csvData => {
        const rows = csvData.split('\n');
        const columnNames = rows[0].split(',');
        for (let i = 1; i < rows.length; i++) {
          const row = rows[i].split(',');
          const obj = {
            dcode: row[0],
            name: row[1],
            data: []
          };
          for (let j = 2; j < columnNames.length; j++) {
            const year = columnNames[j].trim();
            const value = row[j].replace('%', '').trim();
            obj.data.push({ [year]: value });
          }
          this.jsonData.push(obj);
        }
        this.nameFilter = 'พระนคร'
        this.startYear = 2550
        this.endYear = 2559
        })
      .catch(error => {
        console.error("Error reading CSV file:", error);
      });
  },
  watch: {
    nameFilter: function() {
      this.filterData()
    },
    startYear: function() {
      this.filterData()
    },
    endYear: function() {
      this.filterData()
    },
  },
  methods: {
    filterData() {
      if(this.startYear > this.endYear) {
        this.startYear = this.endYear
      }

      this.filteredData = this.jsonData.filter(item => item.name.includes(this.nameFilter)).map(
        item => ({
          ...item,
          data: item.data.filter(entry => {
            const year = Object.keys(entry)[0]
            return year >= this.startYear && year <= this.endYear;
          })
        })
      )

    },
  },
}
</script>

<template>

  <section class="section">
    <h1>สถิติประชากรกรุงเทพฯ พ.ศ. 2550 - 2559</h1>
  </section>

  <section class="section">
    <h3>ลักษณะพื้นที่</h3>
    <p>กรุงเทพฯ เป็นจังหวัดที่มีประชากรมากที่สุดใน ประเทศไทยหากรวมประชากรแฝงที่ไม่ปรากฏในทะเบียนและคนที่เดินทางมาทำงานในตอนกลางวันด้วยแล้ว คาดว่าจะสูงถึงเกือบเท่าตัวของ ประชากรที่ปรากฏในทะเบียน เราจึงเรียก กรุงเทพฯ ว่าเป็น <a href="https://en.wikipedia.org/wiki/Megacity">“อภิมหานคร (megacity)”</a> คือมีประชากรตั้งแต่ 10 ล้านคนขึ้นไป</p>
    <p>อัตราเพิ่มของประชากรกรุงเทพฯ อยู่ระดับเกือบ 1% และเริ่มลดลงในปี 2559 ดังแสดงในแผนภูมิต่อไปนี้</p>
  </section>

  <section class="section">
    <h3>การเติบโต</h3>
    <form style="flex-wrap: wrap; flex-direction: column;">
        <label for="areaName" style="margin-right: 2em;">เขต</label>
        <select name="areaName" id="areaName" style="margin-right: 5em;" v-model="nameFilter">
          <option v-for="item in jsonData" :value="item.name" :key="item.dcode">{{ item.name }}</option>
        </select>

        <label for="startYear" style="margin-right: 2em;">ตั้งแต่</label>
        <select name="startYear" id="startYear" v-model="startYear">
          <option v-for="year in years" :value="year">{{ year }}</option>
        </select>
      
        <label for="startYear" style="margin-right: 2em; margin-left: 2em;">ถึง</label>
        <select name="endYear" id="endYear" v-model="endYear">
          <option v-for="year in years" :value="year">{{ year }}</option>
        </select>
    </form>

    <div v-if="filteredData.length > 0">
      <GraphComponent :data="filteredData[0].data" :min="Math.min(...filteredData[0].data.map(item => parseFloat(Object.values(item)[0])))" :max="Math.max(...filteredData[0].data.map(item => parseFloat(Object.values(item)[0])))" style="margin-top: 2em; margin-bottom: 2em; margin-left: 2em;"></GraphComponent>
    </div>

  </section>

  <section class="section">
    <h3>แหล่งข้อมูล</h3>
    <ul>
      <li>
        <a href="https://stat.bora.dopa.go.th/stat/statnew/statMONTH/statmonth/" target="_blank">
          สำนักบริหารการทะเบียน กรมการปกครอง กระทรวงมหาดไทย, จำนวนประชากร, สำนักบริหารการทะเบียน กรมการปกครอง กระทรวงมหาดไทย, Editor. 2564: กรุงเทพฯ.
        </a>
      </li>
      <li>
        <a href="http://www.nso.go.th/" target="_blank">
          สำนักงานสถิติแห่งชาติ, การสำรวจภาวะเศรษฐกิจและสังคมของครัวเรือน พ.ศ. 2563 สำนักงานสถิติแห่งชาติ, Editor. 2563: กรุงเทพฯ
        </a>
      </li>
      <li>
        <a href="http://www.price.moc.go.th/" target="_blank">
          สำนักดัชนีเศรษฐกิจการค้า กระทรวงพาณิชย์, ข้อมูลดัชนีราคาผู้บริโภคทั่วไป, สำนักดัชนีเศรษฐกิจการค้า กระทรวงพาณิชย์, Editor. 2563: กรุงเทพฯ.
        </a>
      </li>
    </ul>
  </section>
</template>

<style>
h1 {
  font-size: 28px;
  font-weight: bold;
}

h2 {
  font-size: 24px;
  font-weight: bold;
}

h3 {
  font-size: 18px;
  font-weight: bold;
}

h4 {
  font-size: 16px;
  font-weight: bold;
}

p {
  font-size: 14px;
}

h1, h2, h3, h4, p {
  margin-bottom: 1em;
}

section{
  margin-top: 2em;
}
</style>