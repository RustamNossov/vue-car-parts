<template>
  <div  class="home" >
    <MainPage :onTransferProps="onTransferProps"  :onExportToExcel="onExportToExcel" :tableElements="tableElements"/>
    <Table 
        :onTransferProps="onTransferProps" 
        :tableElements="tableElements" 
        :loadingStatus="loadingStatus"/>

    <Modal
      v-if="actionType"
      :recordId="recordId"
      :actionType="actionType"
      :elementsForUpdate="elementsForUpdate"
      :onHandleOk="onHandleOk"
      :addNewRecord="addNewRecord"
      :resetActionType="resetActionType"
      :loadingStatus = "loadingStatus"
     
    />
  </div>
</template>

<script>
import MainPage from "@/components/MainPage";
import Table from "@/components/Table";
import Modal from "@/components/Modal";
import { fetching } from "../services/fetching";

export default {
  name: "Home",
  components: {
    MainPage,
    Table,
    Modal,
  },
  data: () => ({
    recordId: null,
    actionType: null,
    tableElements: [],
    elementsForUpdate: [],
    loadingStatus: "idle",
  }),

  methods: {
    onTransferProps(recordId, actionType) {
      this.recordId = recordId;
      this.actionType = actionType;
    },
    resetActionType() {
      this.actionType = null;
    },
    onExportToExcel() {

        var element = document.getElementById('my-table');
        html2pdf(element, 'canvas');

    },

    //=========== Fetching data for table =============
    getData() {
      this.loadingStatus = "loading";
      fetching("http://localhost:3001/cars")
        .then((data) => {
          this.tableElements = this.prepareData(data);
          this.elementsForUpdate = data;
          this.loadingStatus = "idle";
        })
        .catch(()=>this.loadingStatus = "error");
    },
    //==========Delete record ==================
    deleteRecord(recordId) {
      this.loadingStatus = "loading";
      fetching(`http://localhost:3001/cars/${recordId}`, "DELETE")
        .then(() => {
          this.loadingStatus = "idle";
        })
        .catch(()=>this.loadingStatus = "error");
    },

    onHandleOk(recordId, recordLevel) {

      if (recordLevel === "3") {
        this.deleteRecord(recordId);
      } else {
        //==========Удаление дочерних элементов===========
        const candidatesForDeleting = [recordId];
        this.tableElements
          .filter((item) => item.parent === recordId)
          .forEach((firstLevelChildItem) => {
            const firstLevelChildrenId = firstLevelChildItem.id;
            candidatesForDeleting.push(firstLevelChildrenId);

            if (recordLevel === "1") {
              this.tableElements
                .filter((item) => item.parent === firstLevelChildrenId)
                .forEach((secondLevelChildItem) => {
                  const secondLevelChildrenId = secondLevelChildItem.id;
                  candidatesForDeleting.push(secondLevelChildrenId);
                });
            }
          });

        candidatesForDeleting.forEach((item) => this.deleteRecord(item));
      }

      const deletingRow = this.elementsForUpdate.filter(
        (item) => item.id === recordId
      )[0];
      this.updateData(deletingRow, "delete");
    },
    //===========Add new record =============
    addNewRecord(data) {
      this.loadingStatus = "loading";
      fetching("http://localhost:3001/cars", "POST", JSON.stringify(data))
        .then((data) => {
          this.loadingStatus = "idle";
          this.updateData(data);
        })
        .catch(()=>this.loadingStatus = "error");
    },
    //============Change Data in database=============
    putData(recirdId, body) {
      this.loadingStatus = "loading";
      fetching(
        `http://localhost:3001/cars/${recirdId}`,
        "PUT",
        JSON.stringify(body)
      )
        .then(() => {
          this.loadingStatus = "idle";
          this.getData();
        })
        .catch(()=>this.loadingStatus = "error");
    },

    //=========== Formatting data ============
    prepareData(data) {
      const arr = [];

      data
        .filter((item) => item.level === "1")
        .forEach((firstlevelItem, i) => {
          const flId = i + 1;
          arr.push({
            ...firstlevelItem,
            name: `${flId}. ${firstlevelItem.name}`,
          });
          data
            .filter((item) => item.parent === firstlevelItem.id)
            .forEach((secondLevelItem, x) => {
              const slId = x + 1;
              arr.push({
                ...secondLevelItem,
                name: `&nbsp;&nbsp;&nbsp;&nbsp;${flId}.${slId}. ${secondLevelItem.name}`,
              });
              data
                .filter((item) => item.parent === secondLevelItem.id)
                .forEach((thirdLevelItem, j) => {
                  const tlId = j + 1;
                  arr.push({
                    ...thirdLevelItem,
                    name: `&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;${flId}.${slId}.${tlId}. ${thirdLevelItem.name}`,
                  });
                });
            });
        });
      return arr;
    },
    updateData(record, type) {
      const newRecordLevel = +record.level;
      const newRecordParent = record.parent;
      const newRecordSum = +record.summa;
      if (newRecordLevel === 1) {
        this.getData();
        return;
      }
      if (newRecordLevel === 2 || newRecordLevel === 3) {
        let parentSum = this.elementsForUpdate
          .filter((item) => item.parent === newRecordParent)
          .reduce((acc, curr) => {
            return acc + +curr.summa;
          }, 0);

        parentSum =
          type === "delete"
            ? parentSum - newRecordSum
            : parentSum + newRecordSum;

        let parentRecord = this.elementsForUpdate.filter((item) => {
          return item.id === newRecordParent;
        })[0];

        let prentParentId = parentRecord.parent;
        parentRecord = {
          ...parentRecord,
          price: parentSum,
          summa: +parentSum * +parentRecord.amount,
        };
        if (newRecordLevel === 3) {
          let parentParentSum = this.elementsForUpdate
            .filter(
              (item) =>
                item.parent === prentParentId && item.id !== parentRecord.id
            )
            .reduce((acc, curr) => {
              return acc + +curr.summa;
            }, 0);
          parentParentSum =
            type === "delete"
              ? parentParentSum + parentRecord.summa
              : parentParentSum + parentRecord.summa;

          let parentParentRecord = this.elementsForUpdate.filter((item) => {
            return item.id === prentParentId;
          })[0];

          parentParentRecord = {
            ...parentParentRecord,
            price: parentParentSum,
            summa: parentParentSum * +parentParentRecord.amount,
          };

          this.putData(prentParentId, parentParentRecord);
        }
        this.putData(newRecordParent, parentRecord);
      }
    },
  },
  mounted() {
    this.getData();
  },
};
</script>

<style>
.home {
  padding: 4vh 3% 2vh 3%;
  height: 100vh;
}
</style>
