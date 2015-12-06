# AngularJS-Learning-Notes
AngularJS学习笔记摘要

1.日期格式化
$filter('date')(new Date(), 'yyyy-MM-dd');
start: $filter('date')($scope.orderListSearchForm.start,"yyyy-MM-dd"),
end: $filter('date')($scope.orderListSearchForm.end,"yyyy-MM-dd"),

2.等价ng-if的页面判断显示问题
{{orderItem.sku.bundle ? "打包":"单品"}}

3.日期过滤器
{{orderItem.submitDate | date : "yyyy-MM-dd HH:mm:ss"}}

4.下拉选遍历数组
ng-options="o.value as o.name for o in availableStatus"

5.AngularJs table to excel：
.factory('TableToExcel', function ($window) {
    var uri='data:application/vnd.ms-excel;base64,',
        template='<html xmlns:o="urn:schemas-microsoft-com:office:office" xmlns:x="urn:schemas-microsoft-com:office:excel" xmlns="http://www.w3.org/TR/REC-html40"><head><!--[if gte mso 9]><xml><x:ExcelWorkbook><x:ExcelWorksheets><x:ExcelWorksheet><x:Name>{worksheet}</x:Name><x:WorksheetOptions><x:DisplayGridlines/></x:WorksheetOptions></x:ExcelWorksheet></x:ExcelWorksheets></x:ExcelWorkbook></xml><![endif]--></head><body><table>{table}</table></body></html>',
        base64=function(s){return $window.btoa(unescape(encodeURIComponent(s)));},
        format=function(s,c){return s.replace(/{(\w+)}/g,function(m,p){return c[p];})};
    return {
        tableToExcel:function(tableId,worksheetName){
            var table=$(tableId),
                ctx={worksheet:worksheetName,table:table.html()},
                href=uri+base64(format(template,ctx));
            return href;
        }
    };
})

$scope.exportToExcel = function(tableId) {
    var exportHref = TableToExcel.tableToExcel(tableId,'admin list');
    $timeout(function(){location.href = exportHref;},100);
}

<button class="btn btn-link" ng-click="exportToExcel('#table1')">
    <span class="glyphicon glyphicon-share"></span> Export to Excel
</button>

6.Angular+Bootstrap可编辑表格：
bower install angular-xeditable
<link href="bower_components/angular-xeditable/dist/css/xeditable.css" rel="stylesheet">
<script src="bower_components/angular-xeditable/dist/js/xeditable.js"></script>

7.全选/反选
$scope.isCheckedAll = false;
$scope.checkAll = function() {
    if(!($scope.isCheckedAll)){
        angular.forEach($scope.zones, function(value, key){  
            $scope.formData.zoneIds.push(value.id);
        });
        $scope.isCheckedAll = true;
    }else{
        $scope.formData.zoneIds = [];
        $scope.isCheckedAll = false;
    }
};

8.搜索条件表单
<div class="row">
    <div class="col-lg-12">
       <form rol="form" class="form-inline">
          <div class="form-group">
            <input type="text" class="form-control" placeholder="用户名">&nbsp;&nbsp;
          </div>
          <div class="form-group">
             <select class="form-control">
              <option>--全部--&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</option>
              <option>在职</option>
              <option>离职</option>
             </select>
             &nbsp;&nbsp;
          </div>
          <button type="submit" class="btn btn-primary">&nbsp;&nbsp;搜&nbsp;&nbsp;索&nbsp;&nbsp;</button>
          <button type="button" class="btn btn-success" ui-sref="admin.addStaff">添加员工</button>
       </form>
    </div>
</div>

9.ui-select:
http://plnkr.co/edit/a3KlK8dKH3wwiiksDSn2  
http://plnkr.co/edit/a3KlK8dKH3wwiiksDSn2?p=preview

<ui-select ng-model="zone.selected" theme="bootstrap" reset-search-input="false" style="width: 100%;">
    <ui-select-match placeholder="Select..." >{{$select.selected.displayName}}</ui-select-match>
    <ui-select-choices repeat="zone in zones | filter: $select.search">
       <span ng-bind-html="zone.displayName | highlight: $select.search"></span>
       <small ng-hide="true" ng-bind-html="zone.id | highlight: $select.search"></small>
    </ui-select-choices>
</ui-select>

10.校验语法之ng-trim ng-minlength ng-maxlength required ng-pattern等标签
表单中的输入框，你可以使用上面的标签来实现对用户输入的校验。

<form name=”yourForm”>
  <input type=”text” name=”inputText” required ng-trim=”true” ng-model=”userNum” ng-pattern=”/^[0-9]*[1-9][0-9]*$/” ng-maxlength=”6〃 maxlength=”6〃/>
</form>

你可以通过 $scope.yourForm.inputText.$error.required 来判断输入框是否为空

你可以通过 $scope.yourForm.inputText.$invalid 来判断输入的内容是否满足ng-pattern，ng-maxlength，maxlength

你通过$scope.userNum获得的输入内容是去掉前后空白的，因为ng-trim的存在。

11.异步请求之$http对象
AngularJS提供了一个类似jquery的$.ajax的对象，用于异步请求。
在AngularJS中对异步操作是推崇至极的，所以$http的操作都是异步的不像jquery.ajax里还提供了async参数。

$http({method : ‘POST’,params : { id:123}, data:{name:’john’,age:27}, url : “/mypath”})
.success(function(response, status, headers, config){
  //do anything what you want;
})
.error(function(response, status, headers, config){
  //do anything what you want;
});

如果你是POST请求，params里的数据会帮你拼到url后面，data里的数据会放到请求体中。

12.指令的使用范围
restrict 的取值可以有三种：
  A 用于元素的 Attribute，这是默认值
  E 用于元素的名称
  M 注释comment
  C 用于 CSS 中的 class

13.模态弹窗
<div class="modal fade" id="myModal" tabindex="-1" role="dialog" aria-labelledby="myModalLabel" aria-hidden="true">
   <div class="modal-dialog">
       <div class="modal-content">
           <div class="modal-header">
               <button type="button" class="close" data-dismiss="modal" aria-hidden="true">&times;</button>
               <h4 class="modal-title" id="myModalLabel">提示信息</h4>
           </div>
           <div class="modal-body">输入密码不正确，请重新输入。</div>
           <div class="modal-footer">
               <button type="button" class="btn btn-success" data-dismiss="modal">确认</button>
               <button type="button" class="btn btn-primary" data-dismiss="modal">取消</button>
           </div>
       </div>
   </div>
 </div>

<button type="submit" class="btn btn-primary" ng-disabled="changePasswordForm.$invalid" data-toggle="modal" data-target="#myModal" data-backdrop="static">&nbsp;&nbsp;保&nbsp;&nbsp;存&nbsp;&nbsp;</button>
