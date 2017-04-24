<%@ page language="java" pageEncoding="UTF-8"%>
<%@ taglib uri="http://java.sun.com/jstl/core_rt" prefix="c"%>
<%@ taglib uri="http://java.sun.com/jstl/fmt" prefix="fmt"%>
<%@ page import="java.util.Date" %>
<%
Date date = new Date();
long tstamp =  date.getTime();			
%>
<script src="${pageContext.request.contextPath}/js/validator.js?ts=<%= tstamp%>"></script>
<script src="${pageContext.request.contextPath}/js/ibound.js?ts=<%=tstamp%>"></script>

<script type="text/javascript">

/************************ Global  변수 선언 영역 START **********************/
	var g_selectGrid = "#gridMain";	// 그리드 DIV를 담는 변수(초기 값 : 마스터 그리드)

	var g_checkedMasterIds = {};	// 마스터 그리드 체크박스 저장변수(배열)
	var g_checkedSlaveIds  = {};	// 슬레이브 그리드 체크박스 저장변수(배열)
	
	var g_srchParams      = {};		// 마스터 그리드 검색 시 검색조건 값을 담는 변수(배열)
	var g_srchSlaveParams = {};		// 슬레이브 그리드 검색 시 검색조건 값(마스터 그리드 값)을 담는 변수(배열)
	
	var g_mainDataSource = null;	// 마스터 그리드 KENDO 데이터 소스 저장변수
	var g_slaveDataSource  = null;	// 슬레이브 그리드 KENDO 데이터 소스 저장변수
	
	var g_masterUid  	= null;		// 슬레이브 그리드 수정 값 발생 시 선택된 마스터 그리드의 UID를 담을 변수
	var g_masterRow  	= null;		// 슬레이브 그리드 수정 값 발생 시 선택된 마스터 그리드의 TR(ROW)을 담을 변수
	var g_masterBefUid  = null;		// 마스터의 동일한 ROW(TR)의 다른 CELL(TD) 클릭 시 슬레이브 그리드 조회를 막기위한 값을 담을 변수 
	
	var g_paramList       = [];		// 마스터 그리드의 CUD에 사용할 변수(리스트)
	var g_slaveParamList  = [];		// 슬레이브 그리드의 CUD에 사용할 변수(리스트)
	var g_changeDateParam = [];
	
	var g_centerCd    = null;
	var g_shipperCd   = null;
	var g_inboundDate = null;
	var g_inboundNo   = null;
	var g_inboundNo1111123   = null;
	
	var g_slaveGridCallFalg = true;
	
	/* 장원봉 20161229 팝업(dirty포함)처리, 그리드 내 콤보박스 처리 */
	var g_currentRow      = null;
	var g_slaveCurrentRow = null;
	var g_popupCurrentRow = null;	// 팝업을 통해서 바인딩 되는 경우 현재 Row를 가져오는 부분 수정 / 팝업 OR 팝업이 아닌경우로 나뉨 20170327.김범수
	var g_popupCurrentUid = null;
	
	/* 장원봉 20161230  그리드 내 콤보박스 사용변수*/ 
	var g_inboundDivCode   = new Array();		
	var g_prodStsCode      = new Array();
	var g_shelfLifeDivCode = new Array();
	var g_storageDivCode   = new Array();
	
	var g_srchMsgPrint     = "N";
	var g_supplyPopYn      = "N";
	var g_prodPopYn        = "N";
	
	var g_lGridSelectSeq = null;
	var g_mGridSelectSeq = null;
	var g_sGridSelectSeq = null;	
	var g_test = 0;
	var inboundStatus = 'INBOUND_STATUS_A';	// 입고대기 상태 20170105.김범수 추가

	/* 검색로딩 */
	var g_async = true;
	
	// var g_prodPopupChkCnt = 0;
	
/************************ Global  변수 선언 영역 END ************************/

/************************ document.ready 영역 START *************************/
	$(document).ready(function () {
		
		console.log("CENTER CD : "+GLB_USER.centerCd);
		console.log("SHIPPER_CD : "+GLB_USER.shipperCd);
		
		var v_params = {}
		g_srchParams['callFlag'] = "init";
		g_srchSlaveParams['callFlag'] = "init";
		
		/* 장원봉 20161229 팝업(dirty포함)처리, 그리드 내 콤보박스 처리 */
		<c:if test="${not empty inboundDivCode}">			//마스터 그리드 입고구분 콤보박스
			<c:forEach items="${inboundDivCode}" var="data" varStatus="status">
				g_inboundDivCode.push({id : "${data.sCommonCd}", name : "${data.sCommonNm}"});			
			</c:forEach>
		</c:if>
		
		<c:if test="${not empty prodStsCode}">				//슬레이브 그리드 상품상태 콤보박스
			<c:forEach items="${prodStsCode}" var="data" varStatus="status">
				g_prodStsCode.push({id : "${data.sCommonCd}", name : "${data.sCommonNm}"});			
			</c:forEach>
		</c:if>
	
		<c:if test="${not empty shelfLifeDivCode}">			//슬레이브 그리드 유통기한구분 콤보박스
			<c:forEach items="${shelfLifeDivCode}" var="data" varStatus="status">
				g_shelfLifeDivCode.push({id : "${data.sCommonCd}", name : "${data.sCommonNm}"});			
			</c:forEach>
		</c:if>

		<c:if test="${not empty storageDivCode}">			//슬레이브 그리드 보관구분 콤보박스
			<c:forEach items="${storageDivCode}" var="data" varStatus="status">
				g_storageDivCode.push({id : "${data.sCommonCd}", name : "${data.sCommonNm}"});			
			</c:forEach>
		</c:if>
		
		fn_masterGrid();		// master 그리드 처리.
		fn_slaveGrid();			// slave 그리드 처리.
		fn_gridSplit();			// 그리드 Split 처리.
		fn_init();				// 초기 이벤트 바인딩
		fnDayChk();				// datapicker처리.


		
		/* 검색로딩 */
		g_async = false;
		
		
	});// document.ready 영역 END 
	

/************************ Function 영역 START *******************************/	
	Date.prototype.yyyymmdd = function() {
        var mm = this.getMonth() + 1; // getMonth() is zero-based
        var dd = this.getDate();

        return [this.getFullYear(),
	                (mm>9 ? '' : '0') + mm,
	                (dd>9 ? '' : '0') + dd
               ].join('');
    };
    
    String.prototype.trim = function() {
        return this.replace(/(^\s*)|(\s*$)/gi, "");
    }    
	
	/*======================================================*
	 * - Function 명   : fn_init                            *
	 * - Function 기능 : 1. 검색버튼 마우스 우클릭 처리     *
	 *                   2. 선택 그리드 CSS(tit_bg) 처리    *   
	 *======================================================*/	
	function fn_init() {
		
		var v_grid = $("#gridMain").data("kendoGrid");
		var v_sGrid = $("#gridSlave").data("kendoGrid");
		
		$("#btn_search").on("contextmenu", function(e) {		// 마우스 우클릭 Default context menu Close 처리
			e.preventDefault();
		});
		$(".right_slide").on("contextmenu", function(e) {		// 마우스 우클릭 Default context menu Close 처리변경함 20161219.김범수
			e.preventDefault();
		});
		
		$("#btn_search").mousedown(function(e) {				// 마우스 우클릭 Customer Menu 처리
			if (e.which == 3) {
				if ($(".right_slide").css("display") != "block") {
					// $(".right_slide").css("display","block");	// 판넬 우측에 실선이 남는 현상이 있어서 변경함 20161219.김범수
					$('.right_slide').show();
				}
			}
			e.stopPropagation();
		});	
		
		$("#content-area").mousedown(function(e) {				// 검색판넬이 아닌곳 클릭했을경우 판넬 Hide 처리 20161219.김범수
			if (e.which == 1) {
				if ($(".right_slide").css("display") == "block") {
					// $(".right_slide").css("display","none");
					$('.right_slide').hide();
				} 
			}
			e.stopPropagation();
		});	
		
    	$("#top-pane").click(function(){						// 마스터 그리드 클릭 CSS 적용 및 마스터 그리드 ID 전역변수 저장
    		$(".title_area").removeClass("tit_bg");
    		$(this).children(".t_inner").children(".title_area").addClass("tit_bg");
    		g_selectGrid = "";
    		g_selectGrid = "#gridMain";
    	});	
    	
    	$("#bottom-pane").click(function(){						// 슬레이브 그리드 클릭 CSS 적용 및 슬레이브 그리드 ID 전역변수 저장
    		$(".title_area").removeClass("tit_bg");
    		$(this).children(".b_inner").children(".title_area").addClass("tit_bg");
    		g_selectGrid = "";
    		g_selectGrid = "#gridSlave";
    	});	 
    	
    	
    	/* 장원봉 20161229 팝업(dirty포함)처리, 그리드 내 콤보박스 처리 */
/*         $(v_grid.tbody).on("dblclick", "td", function (e) {		// 더블클릭 공급처코드 팝업처리
            var v_row = $(this).closest("tr");
            var v_rowIdx = $("tr", v_grid.tbody).index(v_row);
            var v_colIdx = $("td", v_row).index(this);
	        
            if (v_colIdx == 4) {
				var v_ssRaw = g_slaveDataSource.data();			// 슬레이브 그리드 데이터
	    	    var v_ssLength = v_ssRaw.length;				// 슬레이브 그리드 RECORD COUNT
	    	    
	    	    if (v_ssLength > 0) {							// 슬레이브 그리드 데이터가 존재하면..
	    		    var v_ssItem, j;
	    		    for (j = v_ssLength - 1; j >= 0; j--) {
	    		    	v_ssItem = v_ssRaw[j];
    		    		if (v_ssItem.crud != "C") {
    		    	    	fn_confirmPopupOpen("연관 데이터가 존재하여 수정이 불가능 합니다.", "입고대기");
    				        return;
    				        
    		    		} else {
    		    			if (v_ssItem.prodCd != "" && v_ssItem.prodCd != null) {
        		    	    	fn_confirmPopupOpen("연관 데이터가 존재하여 수정이 불가능 합니다.", "입고대기");
        				        return;
    		    			} else {
    			    	    	g_supplyPopYn   = "Y";
    			    	    	g_currentRow    = v_row;
    			    	    	
    			    	    	var v_dataItem = v_grid.dataItem(v_grid.select());
    			        		var v_param = "shipperCd=" + v_dataItem.shipperCd;
    			        		$("#gridSupplyPopup").empty();
    			    	    	fn_openPopup("#gridSupplyPopup", "/common/popup/initSupplyPopup.do?" + v_param, "공급처 선택", "500px", "400px");
    		    			}
    		    		}
	    		    }

	    	    } else {										// 슬레이브 그리드 데이터 미존재하면 공급처 선택 팝업 OPEN
	    	    	g_supplyPopYn   = "Y";
	    	    	g_currentRow    = v_row;
	    	    	
	    	    	var v_dataItem = v_grid.dataItem(v_grid.select());
	        		var v_param = "shipperCd=" + v_dataItem.shipperCd;
	        		$("#gridSupplyPopup").empty();
	    	    	fn_openPopup("#gridSupplyPopup", "/common/popup/initSupplyPopup.do?" + v_param, "공급처 선택", "500px", "400px");
	    	    }
            }
        }); */
    	
        /* 현재 사용하지 않아서 주석처리 20170327.김범수
        $(v_sGrid.tbody).on("dblclick", "td", function (e) {		// 더블클릭 상품코드 팝업처리
        	var v_dataItem = v_grid.dataItem(v_grid.select());
        	
	        if (v_dataItem == null) {
	    	    	fn_confirmPopupOpen("입고대기 마스터 그리드의 레코드를 선택해 주십시오.", "입고대기");
			        return;
	        	}
	        	
	        	if (v_dataItem.supplyCd == null || v_dataItem.supplyCd == '' ) {
	    	    	fn_confirmPopupOpen("입고대기 마스터 그리드의 공급처코드를 입력해 주십시오.", "입고대기");
			        return;
	        	} 
	        	
	            var v_sRow = $(this).closest("tr");
	            var v_sRowIdx = $("tr", v_sGrid.tbody).index(v_sRow);
	            var v_sColIdx = $("td", v_sRow).index(this);
		        
	            if (v_sColIdx == 3) {
	            	
	            	g_slaveCurrentRow = v_sRow;
	            	var v_dataItem = v_grid.dataItem(v_grid.select());
	        		var v_sParam = "shipperCd=" + v_dataItem.shipperCd;
	        		v_sParam += "&" + "supplyCd" + "=" + v_dataItem.supplyCd;
	        		$("#gridProductPopup").empty();
				   	fn_openPopup("#gridProductPopup", "/common/popup/initProdPopup.do?" + v_sParam , "상품 선택", "610px", "400px");
	            }
        });     	
         */
        v_grid.thead.on('mousedown', function (e) {				// 마스터 그리드 타이틀 클릭(SORT) 시 수정된 레코드가 있다면 처리불가
	    	if (!fn_modifyDataCheck(g_mainDataSource, v_grid, "title", null)) {
	    		fn_confirmPopupOpen("그리드에 변경한 레코드가 존재합니다.\n저장 후 처리 가능합니다.", "입고대기");
	    		return;
	    	}
        });
        
        v_sGrid.thead.on('mousedown', function (e) {			// 슬레이브 그리드 타이틀 클릭(SORT) 시 수정된 레코드가 있다면 처리불가
	    	if (!fn_modifyDataCheck(g_slaveDataSource, v_sGrid, "title", null)) {
	    		fn_confirmPopupOpen("상세정보에 변경한 레코드가 존재합니다.\n저장 후 처리 가능합니다.", "입고대기");
	    		return;
	    	}
        });
        
        $(v_grid.tbody).on("click", "td", function (e) {
			console.log("click function");	        
        });
        
	}
	
	/*======================================================*
	 * - Function 명   : fn_prodPopup                        *
	 * - Function 기능 : 상품팝업 버튼처리                       *   
	 *======================================================*/
	function fn_prodPopup() {
		
		var v_grid = $("#gridMain").data("kendoGrid");
		var v_sGrid = $("#gridSlave").data("kendoGrid");
		
    	var v_dataItem = v_grid.dataItem(v_grid.select());
    	
        if (v_dataItem == null) {
    	    	fn_confirmPopupOpen("입고대기 마스터 그리드의 레코드를 선택해 주십시오.", "입고대기");
		        return;
        	}
        	
			if (v_dataItem.preNo != null && v_dataItem.preNo != '') {
 		    	fn_confirmPopupOpen("입고예정으로 부터 생성된 데이터는 상품 변경이 불가능 합니다.", "입고대기");
 		        return; 				
 			}
			
        	if (v_dataItem.supplyCd == null || v_dataItem.supplyCd == '' ) {
    	    	fn_confirmPopupOpen("입고대기 마스터 그리드의 공급처코드를 입력해 주십시오.", "입고대기");
		        return;
        	} 
            var v_sRow = v_sGrid.select();	
           	g_slaveCurrentRow = v_sRow;
           	
           	var v_dataItem = v_grid.dataItem(v_grid.select());
       		var v_sParam = "shipperCd=" + v_dataItem.shipperCd;
       		v_sParam += "&" + "supplyCd" + "=" + v_dataItem.supplyCd;
       		v_sParam += "&" +"gdbinfo" + "=" + GLB_USER.dbinfo;
       		$("#gridProductPopup").empty();
		   	fn_openPopup("#gridProductPopup", "/common/popup/initProdPopup.do?" + v_sParam , "상품 선택", "610px", "400px");
	}
	
	/*======================================================*
	 * - Function 명   : fn_supplyPopup                     *
	 * - Function 기능 : 공급처 팝업버튼 처리                   *   
	 *======================================================*/
	function fn_supplyPopup() {
		
		var v_grid = $("#gridMain").data("kendoGrid");
		var v_sGrid = $("#gridSlave").data("kendoGrid");
		
		var v_ssRaw = g_slaveDataSource.data();			// 슬레이브 그리드 데이터
	    var v_ssLength = v_ssRaw.length;				// 슬레이브 그리드 RECORD COUNT
	    
	    var v_row = v_grid.select();
	    
	    // 로직처리 수정 20170325.김범수	
    	g_supplyPopYn   = "Y";
		g_currentRow    = v_row;
		var v_dataItem = v_grid.dataItem(v_row);
		// console.log('@@@2  ' + v_dataItem.inboundDcnt);
		if(0 < v_dataItem.inboundDcnt){
	    	fn_confirmPopupOpen("연관 데이터가 존재하여 수정이 불가능 합니다.", "입고대기");
	        return;
	    }
		
		var v_param = "shipperCd=" + v_dataItem.shipperCd;
		v_param += "&" +"gdbinfo" + "=" + GLB_USER.dbinfo;
		$("#gridSupplyPopup").empty();
		
    	fn_openPopup("#gridSupplyPopup", "/common/popup/initSupplyPopup.do?" + v_param, "공급처 선택", "500px", "355px");
    	// end 20170325.김범수
		
		/* 로직처리 수정으로 인한 주석처리 20170325.김범수
	    if (v_ssLength > 0) {							// 슬레이브 그리드 데이터가 존재하면..
		    var v_ssItem, j;
		    for (j = v_ssLength - 1; j >= 0; j--) {
		    	v_ssItem = v_ssRaw[j];
	    		if (v_ssItem.crud != "C") {
	    	    	fn_confirmPopupOpen("연관 데이터가 존재하여 수정이 불가능 합니다.", "입고대기");
			        return;
			        
	    		} else {
	    			if (v_ssItem.prodCd != "" && v_ssItem.prodCd != null) {
		    	    	fn_confirmPopupOpen("연관 데이터가 존재하여 수정이 불가능 합니다.", "입고대기");
				        return;
	    			} else {
		    	    	g_supplyPopYn   = "Y";
		    	    	g_currentRow    = v_row;
		    	    	
		    	    	var v_dataItem = v_grid.dataItem(v_grid.select());
		        		var v_param = "shipperCd=" + v_dataItem.shipperCd;
		        		$("#gridSupplyPopup").empty();
		    	    	fn_openPopup("#gridSupplyPopup", "/common/popup/initSupplyPopup.do?" + v_param, "공급처 선택", "500px", "355px");
	    			}
	    		}
		    }

	    } else {										// 슬레이브 그리드 데이터 미존재하면 공급처 선택 팝업 OPEN
	    	g_supplyPopYn   = "Y";
	    	g_currentRow    = v_row;
	    	
	    	var v_dataItem = v_grid.dataItem(v_grid.select());
    		var v_param = "shipperCd=" + v_dataItem.shipperCd;
    		$("#gridSupplyPopup").empty();
	    	fn_openPopup("#gridSupplyPopup", "/common/popup/initSupplyPopup.do?" + v_param, "공급처 선택", "500px", "355px");
	    }		
		 */
	}
	
	/* 장원봉 20161229 공급처 코드 다이렉트 입력 시..처리 */
	/*======================================================*
	 * - Function 명   : fn_getSupplyCd                     *
	 * - Function 기능 : 공급처 Direct 조회.                *   
	 *======================================================*/
	/*  현재 사용하지 않아서 주석처리 20170327.김범수
	function fn_getSupplyCd(param) {
		
		$.ajax({
			type :"post",
			url  : "/common/popup/selectSupplyCdPopup.do",
			datatype : "json",
			data     : param,
	        beforeSend : function(request){
	        	request.setRequestHeader("guserid", GLB_USER.userid); 
	        	request.setRequestHeader("gdbinfo", GLB_USER.dbinfo);
	        	request.setRequestHeader("guserkey", GLB_USER.userkey);
	        },				
			success : function(result) {
				if (result.codeSupplyCd.length > 0) {
					var v_param = {};
					v_param['flag'] = "supply";
					v_param['supplyCd'] = result.codeSupplyCd[0].code;
					v_param['supplyNm'] = result.codeSupplyCd[0].codeNm;
					
					fn_setChildValue(v_param);
					
				} else {
					var v_param = {};
					v_param['flag'] = "supply";
					v_param['supplyCd'] = null;
					v_param['supplyNm'] = null;
					
					fn_setChildValue(v_param);
					$("#gridMain").data("kendoGrid").closeCell();
				}
			},
			error : function(x, o, e) {
				showExceptionError(x, o, e);
			}			
		});
	}
	 */
	/* 장원봉 20161229 상품 코드 다이렉트 입력 시..처리 */
	/*======================================================*
	 * - Function 명   : fn_getProductCd                    *
	 * - Function 기능 : 상품 Direct 조회.                  *   
	 *======================================================*/
	/* 현재 사용하지 않아서 주석처리 20170327.김범수 
	function fn_getProductCd(param) {
		
		$.ajax({
			type :"post",
			url  : "/common/popup/selectProdPopup.do",
			datatype : "json",
			data     : param,
	        beforeSend : function(request){
	        	request.setRequestHeader("guserid", GLB_USER.userid); 
	        	request.setRequestHeader("gdbinfo", GLB_USER.dbinfo);
	        	request.setRequestHeader("guserkey", GLB_USER.userkey);
	        },				
			success : function(result) {
				if (result.data.length > 0) {
					var v_param = {};
					v_param['flag'] = "prod";
					v_param['prodCd'] = result.data[0].prodCd;
					v_param['prodNm'] = result.data[0].prodNm;
						
					v_param['prodPltQty']     = result.data[0].prodPltQty;
					v_param['prodPkgQty']     = result.data[0].prodPkgQty;
					v_param['prodSizeWidth']  = result.data[0].prodSizeWidth;
					v_param['prodSizeDepth']  = result.data[0].prodSizeDepth;
					v_param['prodSizeHeight'] = result.data[0].prodSizeHeight;
					v_param['prodWeigth']     = result.data[0].prodWeigth;
					v_param['prodVol']        = result.data[0].prodVol;
					v_param['shelfLifeDIv']   = result.data[0].shelfLifeDIv;
					v_param['shelfLifeDivNm'] = result.data[0].shelfLifeDivNm;
					v_param['storageDiv']     = result.data[0].storageDiv;
					v_param['storageDivNm']   = result.data[0].storageDivNm;
					
					fn_setChildValue(v_param);
				} else {
					var v_param = {};
					v_param['flag'] = "prod";
					v_param['prodCd'] = null;
					v_param['prodNm'] = null;
					
					fn_setChildValue(v_param);
					$("#gridSlave").data("kendoGrid").closeCell();
				}
			},
			error : function(x, o, e) {
				showExceptionError(x, o, e);
			}				
		});
	 }
	  */
	/*======================================================*
	 * - Function 명   : fn_gridSplit                       *
	 * - Function 기능 : 그리드 Height SPLIT 기능을 한다.   *   
	 *======================================================*/
	function fn_gridSplit () {
		$("#splitter_ud").kendoSplitter({
			orientation: "vertical",
			panes: [
				{ collapsible: true, size: "50%" },
				{ collapsible: true, size: "50%"}
			]
	        ,resize : function(e) {
	        	//Master 그리드 Resize
	        	var v_gridElement = $("#gridMain");
	        	var v_gridContent = $("#top-pane");
	        	
	    		var v_gridHeaderHeight = v_gridElement.children('.k-header').height() + v_gridElement.children('.k-grid-header').height();
	    		var v_containerWidth   = v_gridContent.innerWidth();
	    		var v_containerHeight  = v_gridContent.innerHeight();

	    		var g_titleHeight = $(".title_area").height();
	    		v_gridElement.children(".k-grid-content-locked").height(v_containerHeight - v_gridHeaderHeight - 4 - g_titleHeight);
	    		v_gridElement.children(".k-grid-content").height(v_containerHeight - v_gridHeaderHeight - 4 - g_titleHeight);
	    		
	        	//Slave 그리드 Resize
	        	var v_gridSlaveElement = $("#gridSlave");
	        	var v_gridSlaveContent = $("#bottom-pane");
	        	
	    		var v_gridSlaveHeaderHeight = v_gridSlaveElement.children('.k-header').height() + v_gridSlaveElement.children('.k-grid-header').height();
	    		var v_slaveContainerWidth   = v_gridSlaveContent.innerWidth();
	    		var v_slaveContainerHeight  = v_gridSlaveContent.innerHeight();

	    		v_gridSlaveElement.children(".k-grid-content-locked").height(v_slaveContainerHeight - v_gridSlaveHeaderHeight - 4 - g_titleHeight);
	    		v_gridSlaveElement.children(".k-grid-content").height(v_slaveContainerHeight - v_gridSlaveHeaderHeight - 4 - g_titleHeight);
			}		
		});
	}
	
	/*========================================================================*
	 * - Function 명   : fn_paging                                            *
	 * - Function 기능 : 그리드상단 COUNT 표기처리                            *
	 * - param : grid 마스터 or 디테일의 그리드                               *
	 * - param : dataSource 마스터 or 디테일의 데이터소스                     *  
	 *========================================================================*/	
	function fn_paging(grid, dataSource) {
		
		var v_titleArea, v_oriText, v_str, v_index, v_total, v_select, v_dataItem, v_allData;
		
		v_titleArea = grid.parent().parent().find('.title_area h3');
		v_allData   = dataSource.data();
		
		v_oriText  = v_titleArea.text().split('-')[0];
		
		if(!v_allData || v_allData.length < 1 ) { 
			v_titleArea.text(v_oriText);
			return; 
		}

		var v_gridobj = grid.data("kendoGrid");
		
		v_select   = v_gridobj.select().length > 1 ? v_gridobj.select()[v_gridobj.select().length-1] : v_gridobj.select() ;
		v_dataItem = v_gridobj.dataItem(v_select);
		//v_index    = (v_allData.indexOf(v_dataItem)+1) == 0 ? 1 : (v_allData.indexOf(v_dataItem)+1);
		v_total    = v_gridobj.dataSource.data().length;
		
		var v_raw = v_gridobj.dataSource.data();
	    var v_view = v_gridobj.dataSource.view();
	    var v_length = v_gridobj.dataSource.view().length;
	    var v_item, i, v_count ;
	   
	    if (v_dataItem != null) {
	      for (i = v_length - 1; i >= 0; i--) {
		         v_item = v_view[i];	
		    	 v_count = i;
		    	 if (v_item.id == v_dataItem.id) {
		    		 v_index = v_count + 1;		    		
		         }
	      }
	    } else {
	    	v_index    = (v_allData.indexOf(v_dataItem)+1) == 0 ? 1 : (v_allData.indexOf(v_dataItem)+1);
	    }
		
		
		v_str      = v_oriText + ' - ' + v_index+' / ' + v_total;	
		
		v_titleArea.text('');				
		v_titleArea.text(v_str);			
	}

	/*========================================================================*
	 * - Function 명   : fn_paging_checkbox                                   *
	 * - Function 기능 : 그리드상단 COUNT 표기처리                            *
	 * - param : grid 마스터 or 디테일의 그리드                               *
	 * - param : dataSource 마스터 or 디테일의 데이터소스                     *  
	 *========================================================================*/	
	function fn_paging_checkbox(grid, dataItem, dataSource) {
		
		var v_titleArea, v_oriText, v_str, v_index, v_total, v_select, v_dataItem, v_allData;
		
		v_titleArea = grid.parent().parent().find('.title_area h3');
		v_allData   = dataSource;
		
		var v_gridobj = grid.data("kendoGrid");
		
		v_oriText  = v_titleArea.text().split('-')[0];
		v_dataItem = dataItem;
		v_index    = (v_allData.indexOf(v_dataItem)+1) == 0 ? 1 : (v_allData.indexOf(v_dataItem)+1);
		v_total    = v_gridobj.dataSource.data().length;
		v_str      = v_oriText + ' - ' + v_index+' / ' + v_total;	
		
		v_titleArea.text('');				
		v_titleArea.text(v_str);			
	}
		
	/*======================================================*
	 * - Function 명   : fn_btnNewClick                     *
	 * - Function 기능 : 신규 버튼 클릭에 대한 처리         *   
	 *======================================================*/
	function fn_btnNewClick() {
 		var v_grid = $(g_selectGrid).data("kendoGrid");
 		
 		if (g_selectGrid == "#gridMain") {	//1. 마스터 그리드 신규 생성

 	 		if (!fn_modifyDataCheck(g_mainDataSource,v_grid, "btnNew", null)) {
 		    	fn_confirmPopupOpen("그리드에 이미 신규 레코드가 존재합니다. \n이전 추가작업을 완료하십시오.", "입고대기");
 		        return;
 	 		}
 	 		
 	 		if (!fn_modifyDataCheck(g_slaveDataSource,v_grid, "btnNew", null)) {
 		    	fn_confirmPopupOpen("상세정보에 신규생성 및 변경한 데이터가 존재합니다. \n이전 추가작업을 완료하십시오.", "입고대기");
 		        return;
 	 		}
 	
 	 		
 	 		var v_toDate = new Date();
 	 		v_grid.dataSource.insert(0, {crud:"C", 
 	 			                         id:"new",
 	 			                         centerCd:GLB_USER.centerCd, 
 	 			                         shipperCd:GLB_USER.shipperCd, 
 	 			                         inboundDate : v_toDate, 
 	 			                         inboundDivNm:"입고", 
 	 			                         inboundDiv:"INBOUND_DIV_A",
 	 			                         supplyCd : ""
 	 			                         
 	 		});		// 신규 ROW 생성
 	 		v_grid.editRow($(g_selectGrid +" tr:eq(1)"));								// 신규 생성 ROW FOCUS 			
	 		v_grid.select(v_grid.tbody.find(">tr:first"));								// 신규 생성 ROW SELECTION
 	
			var v_dataItem = v_grid.dataItem(v_grid.tbody.find(">tr:first"));
			var v_currentRow = v_grid.tbody.find("tr[data-uid='" + v_dataItem.uid + "']");
			v_currentRow.find(".ChkBoxMainGrid").prop("checked","checked");					// 신규 생성 ROW 체크박스 체크
        	g_checkedMasterIds[v_dataItem.id] = true;	 
			
 		} else {							//2. 슬레이브 그리드 신규 생성
 			var v_mGrid = $("#gridMain").data("kendoGrid");
 			var v_mDataItem = v_mGrid.dataItem(v_mGrid.select());
 			
 			if (v_mDataItem == null) {
 		    	fn_confirmPopupOpen("입고대기 마스터 레코드를 선택해 주십시오.", "입고대기");
 		        return; 				
 			}
 	    	
 			if (v_mDataItem.preNo != null && v_mDataItem.preNo != '') {
 		    	fn_confirmPopupOpen("입고예정으로 부터 생성된 데이터는 신규생성이 불가능 합니다.", "입고대기");
 		        return; 				
 			}
 			
 	    	if(!fn_slaveGridCreateRowCheck()) {
 	    		return;
 	    	}
 	    	
 	    	v_grid.dataSource.insert(0, {crud:"C", 
 	    		                         centerCd:g_centerCd, 
 	    		                         shipperCd:g_shipperCd, 
 	    		                         inboundDate:v_mDataItem.inboundDate, 
 	    		                         inboundStatus: inboundStatus,
 	    		                         inboundStatusNm: "입고대기",
 	    		                         prodStatus:"PRODUCT_STATUS_A", 
 	    		                         prodStatusNm:"정상", 
 	    		                         lot:"000000",
 	    		                         prodCd : ""
 	    		                         });
 	 		v_grid.editRow($(g_selectGrid +" tr:eq(1)"));								// 신규 생성 ROW FOCUS 	
 	 		
	 		var v_raw = g_slaveDataSource.data();
		    var v_length = v_raw.length;
		    var v_item, i;
		    
		    for(i = v_length - 1; i >= 0; i--) {
		    	v_item = v_raw[i];
		    	if (v_item.crud == "C") {
					var v_currentRow = v_grid.tbody.find("tr[data-uid='" + v_item.uid + "']");
					v_grid.select(v_currentRow);
					v_currentRow.find(".ChkBoxMainGrid").prop("checked","checked");			// 신규 생성 ROW 체크박스 체크
					g_checkedSlaveIds[v_item.id] = true;
					g_masterRow = v_mGrid.tbody.find("tr[data-uid='" + v_mDataItem.uid + "']");
		    	}
		    }
 		}
	}
	

	
	/*======================================================*
	 * - Function 명   : fn_btnSaveClick                    *
	 * - Function 기능 : 저장 버튼 클릭에 대한 처리         *   
	 *======================================================*/
	function fn_btnSaveClick() {
			
		if (!fn_checkboxCheck(g_checkedMasterIds, g_mainDataSource) && !fn_checkboxCheck(g_checkedSlaveIds, g_slaveDataSource)) {
			fn_confirmPopupOpen("저장 대상을 선택해 주십시오.", "입고대기");
			return;
		}
		
		if (!fn_dirtyCheck(g_mainDataSource) && !fn_dirtyCheck(g_slaveDataSource)) {
			fn_confirmPopupOpen("변경된 데이터가 없습니다.\n데이터를 변경 후 저장해주십시오.", "입고대기");
			return;
		}
		
		/* 1. ------------------- 마스터 그리드 체크박스 데이터 ajax 변수에 SET -------------- */
		var v_updateList = [];
		
		var v_paramCheckedIds = {};
		
        v_paramCheckedIds     = g_checkedMasterIds;
		var v_raw = g_mainDataSource.data();
	    var v_view = g_mainDataSource.view();
	    var v_length = v_raw.length;
	    var v_item, i;
	    
	    for (i = v_length - 1; i >= 0; i--) {							// 마스터 그리드 ROW 수 만큼 LOOP
	    	v_item = v_raw[i];											// i 행
	    	if (v_paramCheckedIds[v_item.id]) {						// 마스터 그리드 체크박스에 체크되었고
				
    			if(!fn_validation("master", v_item)) {		// 입력 Validation 체크 함수
    				return;
    			}
    			/* 장원봉 20161229 콤보박스의 코드 가져오기 */
    			var v_inboundDiv = fn_getCommonCode(g_inboundDivCode, v_item.inboundDivNm) ;
    			var v_param = {};
    			v_param['centerCd'] 	  = GLB_USER.centerCd;
        		v_param['shipperCd'] 	  = GLB_USER.shipperCd;
        		v_param['inboundDate'] 	  = v_item.inboundDate.yyyymmdd();
        		v_param['inboundNo'] 	  = v_item.inboundNo;
        		v_param['supplyCd'] 	  = v_item.supplyCd;
        		v_param['inboundDiv'] 	  = v_inboundDiv;
        		v_param['gridSeq']        = v_item.gridSeq;
        		v_param['crud'] 		  = v_item.crud == null ? "U" : "C";	// curd 값은 row 생성(insert) 시에 C로 SET. update는 crud == null
        		v_param['inboundStatus']  = inboundStatus;				// 입고[M]의 진행상태 컬럼은 입고대기 생성시만 사용됨 20170105.김범수
        		
        		v_updateList.push(v_param);							// ajax로 넘길 변수에 수정된 값 저장(마스터 그리드용 변수)
        	}
	    }	
	    
	    /* 2. ------------- 슬레이브 그리드 체크박스 체크 데이터 ajax 변수에 SET ----------------- */
		var v_slaveUpdateList = [];
		var v_sParamCheckedIds = {};
		
		v_sParamCheckedIds     = g_checkedSlaveIds;
		
		var v_grid     = $("#gridMain").data("kendoGrid");
		var v_dataItem = v_grid.dataItem(v_grid.select());	
		
		var v_sRaw = g_slaveDataSource.data();
	    var v_sView = g_slaveDataSource.view();
	    var v_sLength = v_sRaw.length;
	    var v_sItem, j;
	    
	    for (j = v_sLength - 1; j >= 0; j--) {
	    	v_sItem = v_sRaw[j];
	    	
	    	if (v_sParamCheckedIds[v_sItem.id]) {		// 체크박스 체크 시 = true
	        		
    			if(!fn_validation("slave", v_sItem)) {	//입력 Validation 체크 함수
    				return;
    			}
    		
    			var v_prodStatus = fn_getCommonCode(g_prodStsCode, v_sItem.prodStatusNm) ;
    			var v_sParam = {};
    			v_sParam['centerCd'] 	= v_dataItem.centerCd;
    			v_sParam['shipperCd'] 	= v_dataItem.shipperCd;
    			v_sParam['inboundNo'] 	= v_dataItem.inboundNo;
    			
    			v_sParam['inboundDate'] = v_sItem.inboundDate.yyyymmdd();
    			
    			v_sParam['prodCd']        = v_sItem.prodCd;
    			v_sParam['prodNm']        = v_sItem.prodNm;
    			v_sParam['prodStatus']    = v_prodStatus;
    			v_sParam['lot']           = v_sItem.lot;
    			
    			v_sParam['inboundStatus'] = inboundStatus;
    			v_sParam['excQty']        = v_sItem.excQty;
    			v_sParam['inboundSeq']  = v_sItem.inboundSeq;
    			v_sParam['gridSeq']     = v_sItem.gridSeq;
    			v_sParam['lGridSeq']    = v_dataItem.gridSeq;	    			
    			v_sParam['crud'] 		= v_sItem.crud == null ? "U" : "C";
        		v_slaveUpdateList.push(v_sParam);
        	}
	    }
	    /* 3. -------------------- transport.update call ------------------------- */
	    if (v_updateList.length > 0 || v_slaveUpdateList.length > 0) {
			
			$("#confirmDiv .message").empty().text("저장하시겠습니까?");
			$("#confirmDiv").dialog({
				closeOnEscape: false,
				resizable : false,
				width : 320,
				modal : true,
				title : "입고대기",
				buttons : {
					Ok : {
						text : '확인',/*확인*/
						click : function() {
							$(this).dialog('close');
				    		g_paramList.length    = 0;
				    		g_slaveParamList.length = 0;
				    		
				    		g_slaveParamList = v_slaveUpdateList;    		
				    		g_paramList = v_updateList;
				    		g_mainDataSource.transport.update();
						}
					},
					Cancel : {
						text : '취소',/*확인*/
						click : function() {
							$(this).dialog('close');
							return;
						}
					},
				}
			});
	    	
	    } 
	}
	
	/*======================================================*
	 * - Function 명   : fn_btnDeleteClick                  *
	 * - Function 기능 : 삭제 버튼 클릭에 대한 처리         *   
	 *======================================================*/
	function fn_btnDeleteClick() {
		 
		var v_grid = $("#gridMain").data("kendoGrid");
		var v_dataItem = v_grid.dataItem(v_grid.select());	
		
		var v_sGrid = $("#gridSlave").data("kendoGrid");
		
		var alertMsg = "삭제하시겠습니까?";
		
		if(!fn_modifyDataCheck(g_mainDataSource, v_grid, "btnDelete", null)) {
			fn_confirmPopupOpen("그리드에 변경 데이터가 존재합니다. \n이전 작업 완료 후 삭제하십시오.", "입고대기");
			return;
		};
		
		if(!fn_modifyDataCheck(g_slaveDataSource, v_sGrid, "btnDelete", null)) {
			fn_confirmPopupOpen("상세정보에 변경 데이터가 존재합니다. \n이전 작업 완료 후 삭제하십시오.", "입고대기");
			return;
		};
		
		if (!fn_checkboxCheck(g_checkedMasterIds, g_mainDataSource) && !fn_checkboxCheck(g_checkedSlaveIds, g_slaveDataSource)) {
			fn_confirmPopupOpen("삭제 대상을 선택해 주십시오.", "입고대기");
			return;
		}
		
	    /* 1. ------------------- 슬레이브 그리드 체크박스 데이터 ajax 변수에 SET -------------- */
	    
	    g_slaveParamList.length = 0;		
		var v_sParamCheckedIds = g_checkedSlaveIds;

		var v_sRaw = g_slaveDataSource.data();
	    var v_sView = g_slaveDataSource.view();
	    var v_sLength = v_sRaw.length;
	    var v_sItem, j;
	    for (j = v_sLength - 1; j >= 0; j--) {
	    	v_sItem = v_sRaw[j];
	    	
	    	if (v_sParamCheckedIds[v_sItem.id]) {
	    	    if (v_sItem.crud == "C") {
	    	    	v_sItem.set("crud", "D");
	    	    	g_slaveDataSource.remove(v_sItem);
	    	    } else {
	        		var v_sParam = {};
	        		v_sParam['centerCd']    = g_centerCd;
	        		v_sParam['shipperCd']   = g_shipperCd;
	        		v_sParam['inboundDate'] = g_inboundDate.yyyymmdd();
	        		v_sParam['inboundNo']   = g_inboundNo;
	        		v_sParam['inboundSeq']  = v_sItem.inboundSeq;
	        		v_sParam['gridSeq']     = v_sItem.gridSeq;
	    			v_sParam['lGridSeq']    = v_dataItem.gridSeq;	    	
	        		g_slaveParamList.push(v_sParam);	 //변수SET
	    	    }
	    	}
	    }
	    
		/* 2. ------------------- 마스터 그리드 체크박스 데이터 ajax 변수에 SET -------------- */
		var v_paramCheckedIds = {};

		g_paramList.length = 0;
        v_paramCheckedIds  = g_checkedMasterIds;
		
		var v_raw = g_mainDataSource.data();
	    var v_view = g_mainDataSource.view();
	    var v_length = v_raw.length;
	    var v_item, i;
 				
	    for(i = v_length - 1; i >= 0; i--) {
	    	v_item = v_raw[i];
	    	if (v_paramCheckedIds[v_item.id]) {
	    		
	    		var v_ssRaw = g_slaveDataSource.data();		// 슬레이브 그리드 데이터
	    	    var v_ssLength = v_ssRaw.length;			// 슬레이브 그리드 데이터 RECORD COUNT
	    	    
	    	    if (v_item.crud == "C") {
	    	    	v_item.set("crud", "D");
	    	    	g_mainDataSource.remove(v_item);
	    	    	
	    	    } else {
	        		var v_param = {};
	        		v_param['centerCd']    = v_item.centerCd;
	        		v_param['shipperCd']   = v_item.shipperCd;
	        		v_param['inboundDate'] = v_item.inboundDate.yyyymmdd();
	        		v_param['inboundNo']   = v_item.inboundNo;
	        		v_param['preNo']       = v_item.preNo;
	        		
	        		if (v_item.preDate != '' && v_item.preDate != null) {
	        			v_param['preDate']     = v_item.preDate.yyyymmdd();
	        		}
	        		v_param['gridSeq']     = v_item.gridSeq;
	        		g_paramList.push(v_param);				//변수SET
	    	    }
	    	    
	    	    alertMsg = "상세데이터까지 모두 삭제됩니다. 삭제하시겠습니까?";
        	}
	    }	
	    
	    /* 3. ----------------------- transport.destory call ---------------------------*/
	    if (g_paramList.length > 0 || g_slaveParamList.length > 0) {
	    	
			$("#confirmDiv .message").empty().text(alertMsg);
			$("#confirmDiv").dialog({
				closeOnEscape: false,
				resizable : false,
				width : 320,
				modal : true,
				title : "입고대기",
				buttons : {
					Ok : {
						text : '확인',/*확인*/
						click : function() {
							$(this).dialog('close');
							g_mainDataSource.transport.destroy();
						}
					},
					Cancel : {
						text : '취소',/*확인*/
						click : function() {
							$(this).dialog('close');
							return;
						}
					},
				}
			});
    		
	    }		
	}
	
	
	/*======================================================*
	 * - Function 명   : fn_btnSearchClick                  *
	 * - Function 기능 : 검색 버튼 클릭에 대한 처리         *   
	 *======================================================*/
	function fn_btnSearchClick() {
		
		var v_grid = $("#gridMain").data("kendoGrid");
		var v_sGrid = $("#gridSlave").data("kendoGrid");
		if ( !fn_modifyDataCheck(g_mainDataSource, v_grid, "btnSearch", null) ||
				 !fn_modifyDataCheck(g_slaveDataSource, v_sGrid, "btnSearch", null)) {
 			$("#confirmDiv .message").empty().text("그리드에 신규생성 및 변경한 데이터가 존재합니다. \n검색을 진행하시겠습니까?");
			$("#confirmDiv").dialog({
				closeOnEscape: false,
				resizable : false,
				width : 320,
				modal : true,
				title : "입고대기",
				buttons : {
					Ok : {
						text : '확인',/*확인*/
						click : function() {
							$(this).dialog('close');
							g_srchMsgPrint = "Y";
							var v_params = {};
							v_params['centerCd']      = GLB_USER.centerCd;
							v_params['shipperCd']     = GLB_USER.shipperCd;
							v_params['inboundPageFlag']  = 'inboundReady';	// 입고조회 쿼리를 공통으로 사용하기위한 구분자 추가 20170105.김범수
							g_srchParams = v_params;
							g_mainDataSource.read();
							$("#top-pane").trigger("click");
						}
					},
					Cancel : {
						text : '취소',/*확인*/
						click : function() {
							$(this).dialog('close');
							return;
						}
					},
				}
			});
 		} else {
 			g_srchMsgPrint = "Y";
			var v_params = {};
			v_params['centerCd']      = GLB_USER.centerCd;
			v_params['shipperCd']     = GLB_USER.shipperCd;
			v_params['inboundPageFlag']  = 'inboundReady';	// 입고조회 쿼리를 공통으로 사용하기위한 구분자 추가 20170105.김범수
			g_srchParams = v_params;
			g_mainDataSource.read();
			$("#top-pane").trigger("click");
 		}
		
		searchBoxEvent();
	}

	/*====================================================== *
	 * - Function 명   : fn_inboundDateChangeCehck           *
	 * - Function 기능 : 입고실행 시 입고일자 변경여부 체크      *    
	 *====================================================== */
	 
	function fn_inboundDateChangeCheck() {
		var v_grid = $("#gridMain").data("kendoGrid");
		var v_sGrid = $("#gridSlave").data("kendoGrid");
			
		if (!fn_checkboxCheck(g_checkedMasterIds, g_mainDataSource)) {
			fn_confirmPopupOpen("입고대기 그리드의 입고실행 대상을 선택해 주십시오.", "입고대기");
			return;
		}

		if (!fn_modifyDataCheck(g_mainDataSource, v_grid, "btnSearch", null) ||
				 !fn_modifyDataCheck(g_slaveDataSource, v_sGrid, "btnSearch", null) ) {
			fn_confirmPopupOpen("변경된 데이터가 있습니다. 저장 후 처리하십시오.", "입고대기");
			return;
		}
		
		var v_updateList = [];
		var v_paramCheckedIds = {};
		
        v_paramCheckedIds     = g_checkedMasterIds;
		var v_raw = g_mainDataSource.data();
	    var v_view = g_mainDataSource.view();
	    var v_length = v_raw.length;
	    var v_item, i;
	    
	    var v_params = [];
	    var v_params2 = []; //빈 값으로 보낸다.
	    
	    var reqCnt = 0;
	    for (i = v_length - 1; i >= 0; i--) {							// 마스터 그리드 ROW 수 만큼 LOOP
	    	v_item = v_raw[i];											// i 행
	    	if (v_paramCheckedIds[v_item.id]) {	
	    		reqCnt += 1;
	    		
	    		var v_param  = {};
        		v_param['centerCd'] 	 = GLB_USER.centerCd;
        		v_param['shipperCd'] 	 = GLB_USER.shipperCd;
        		v_param['inboundStatus'] = 'INBOUND_STATUS_B';
        		v_param['gridSeq']       = v_item.gridSeq;
        		v_param['inboundEventFlag']  = 'inboundExcute';	// 입고조회 쿼리를 공통으로 사용하기위한 구분자 추가 20170105.김범수
        		v_param['inboundDate'] 	 = v_item.inboundDate.yyyymmdd();
        		v_param['inboundNo'] 	 = v_item.inboundNo;
        		v_params.push(v_param);
	    	}
	    }
	    
	    if (reqCnt > 0) {
	    	
	    	$("#loading").show();
			$.ajax({
				type :"post",
				url  : "/wms/inbound/selectInboundDetailCnt.do",
				datatype : "json",
				data     : {masterModels : kendo.stringify(v_params)},
		        beforeSend : function(request){
		        	request.setRequestHeader("guserid", GLB_USER.userid); 
		        	request.setRequestHeader("gdbinfo", GLB_USER.dbinfo);
		        	request.setRequestHeader("guserkey", GLB_USER.userkey);
		        },					
				success : function(result) {
					if (result.result < 1) {
						
						$("#confirmDiv .message").empty().text("입고대기 상세데이터를 생성해 주십시오.");
						$("#confirmDiv").dialog({
							closeOnEscape: false,
							resizable : false,
							width : 320,
							modal : true,
							title : "입고대기",
							buttons : {
								Ok : {
									text : '확인',/*확인*/
									click : function() {
										$(this).dialog('close');
										$("#loading").hide();
									    return false;
									}
								}
							}
						});
					} else {
						$("#confirmDiv .message").empty().text("입고실행 처리를 합니다. 기입력된 입고일자를 변경하시겠습니까?");
						$("#confirmDiv").dialog({
							closeOnEscape: false,
							resizable : false,
							width : 320,
							modal : true,
							title : "입고대기",
							buttons : {
								Ok : {
									text : '예',
									click : function() {
										$(this).dialog('close');
						        		$("#gridDateChangePopup").empty();
						    	    	fn_openPopup("#gridDateChangePopup", "/common/popup/dateChangePopup.do", "입고일자 변경", "300px", "360px");							
									}
								},
								Cancel : {
									text : '아니오',
									click : function() {
										$(this).dialog('close');
										$("#loading").show();
										fn_btnExcute(null);
									}
								},
							}
						});			
					}
					
				},
				error : function(x, o, e) {
					$("#loading").show();
					if(x.status == 9999) {
						alert("세션이 종료되었습니다. 다시 로그인 하시기 바랍니다.");
						top.location.href = "/";
					} else {
						var msg = "Error \n" + x.status + " : " + o + " : " + e; 
						alert(msg);
						$("#loading").hide();
					}
				}
			});	    	
	    }		    
	}
	
	/*======================================================*
	 * - Function 명   : fn_btnInboundExc                   *
	 * - Function 기능 : 입고실행 버튼                      *   
	 *======================================================*/
	function fn_btnExcute(changeDate) {
		var v_updateList = [];
		
		var v_paramCheckedIds = {};
		
        v_paramCheckedIds     = g_checkedMasterIds;
		var v_raw = g_mainDataSource.data();
	    var v_view = g_mainDataSource.view();
	    var v_length = v_raw.length;
	    var v_item, i;
	    
	    var v_params = [];
	    var v_params2 = []; //빈 값으로 보낸다.
	    
	    for (i = v_length - 1; i >= 0; i--) {							// 마스터 그리드 ROW 수 만큼 LOOP
	    	v_item = v_raw[i];											// i 행
	    	if (v_paramCheckedIds[v_item.id]) {	
	    		var v_param  = {};
        		v_param['centerCd'] 	 = GLB_USER.centerCd;
        		v_param['shipperCd'] 	 = GLB_USER.shipperCd;
        		v_param['inboundStatus'] = 'INBOUND_STATUS_B';
        		v_param['gridSeq']       = v_item.gridSeq;
        		v_param['inboundEventFlag']  = 'inboundExcute';	// 입고조회 쿼리를 공통으로 사용하기위한 구분자 추가 20170105.김범수
        		v_param['inboundDate'] 	 = v_item.inboundDate.yyyymmdd();
        		v_param['inboundNo'] 	 = v_item.inboundNo;
        		
        		if (changeDate != null) {
	        		v_param['newInboundDate'] 	 = changeDate.replace(/-/gi, "");
	           		v_param['orgInboundDate'] 	 = v_item.orgInboundDate.yyyymmdd();
	        		v_param['orgInboundNo'] 	 = v_item.orgInboundNo;
        		} 
        		v_params.push(v_param);
	    	}
	    }
	    if (v_params.length > 0) {
	    	
			$("#confirmDiv .message").empty().text("입고실행 하시겠습니까?");
			$("#confirmDiv").dialog({
				closeOnEscape: false,
				resizable : false,
				width : 320,
				modal : true,
				title : "입고대기",
				buttons : {
					Ok : {
						text : '확인',/*확인*/
						click : function() {
							$("#loading").show();
							$(this).dialog('close');
							fn_updateInboundSts(v_params, v_params2);
						}
					},
					Cancel : {
						text : '취소',/*확인*/
						click : function() {
							$(this).dialog('close');
							$("#loading").hide();
							return;
						}
					},
				}
			});	
	    }
	}
	
	/*======================================================*
	 * - Function 명   : fn_btnExcel                        *
	 * - Function 기능 : 그리드 엑셀다운로드                *   
	 *======================================================*/
	function fn_btnExcel() {
		var v_grid = $(g_selectGrid).data("kendoGrid");
		v_grid.saveAsExcel();
	}
	

	/*======================================================*
	 * - Function 명   : fn_updateInboundSts                 *
	 * - Function 기능 : 상태업데이트                            *   
	 *======================================================*/	
	function fn_updateInboundSts(v_params, v_params2) {
		 
		$.ajax({
			type :"post",
			url  : "/wms/inbound/updateInboundSts.do",
			datatype : "json",
	        beforeSend : function(request){
	        	request.setRequestHeader("guserid", GLB_USER.userid); 
	        	request.setRequestHeader("gdbinfo", GLB_USER.dbinfo);
	        	request.setRequestHeader("guserkey", GLB_USER.userkey);
	        },			
			data     : {masterModels : kendo.stringify(v_params),
						slaveModels  : kendo.stringify(v_params2)	
			},
			success : function(result) {
				if (result.result != "0000") {
			    	fn_confirmPopupOpen(result.result_mesg, "입고대기");
			    	$("#loading").hide();
			    	return;
			    	
				} else {			
					if (result.rtnGridSeq != null) {
						g_lGridSelectSeq = result.rtnGridSeq.lGridSeq;
						g_mGridSelectSeq = result.rtnGridSeq.mGridSeq;
						g_sGridSelectSeq = result.rtnGridSeq.sGridSeq;
					}	
					if (result.result_mesg != null) {
						$("#loading").hide();
						$("#confirmDiv .message").empty().html(result.result_mesg);
						$("#confirmDiv").dialog({
							closeOnEscape: false,
							resizable : false,
							width : 320,
							modal : true,
							title : "입고대기",
							buttons : {
								Ok : {
									text : '확인',/*확인*/
									click : function() {
										$(this).dialog('close');
										$("#loading").hide();
										fn_btnSearchPnlClick("callback");
										g_checkedMasterIds = {};	// 마스터 그리드 체크박스 전역변수 초기화
										g_checkedSlaveIds  = {};	// 슬레이브 그리드 체크박스 전역변수 초기화	
										g_masterUid        = null;	// 초기화
										
																	
									}
								}
							}
						});
					}
				}
			},
			error : function(x, o, e) {
				showExceptionError(x, o, e);
			}				
		});
	}
	/*======================================================*
	 * - Function 명   : fn_btnPrint                        *
	 * - Function 기능 : 그리드 리포트 출력                	*   
	 *======================================================*/
	function fn_btnPrint() {
	/* 
	    var rptParam = JSON.stringify( {
			'arrA' : "valueStrA", 
			'arrB' : "valueStrB", 
			'arrC' : "valueStrC", 
			'arrD' : "valueStrD",
			'arrE' : "valueStrE",
			'arrF' : "valueStrF",
			'arrG' : "valueStrG",
			'arrH' : "valueStrH"
			} );
		///// 필수코딩 /////
		var reportFileNm = 'RPT_PO_PUT.crf';	// 리포트 파일명.확장자포함 ex) RPT_PO_PUT.crf
		fn_btnPrintExcute(reportFileNm, rptParam);
		///// 필수코딩 /////
		 */
	}
	

	/*======================================================*
	 * - Function 명   : fn_selectRow                       *
	 * - Function 기능 : Master 체크박스 클릭/해제 처리     *   
	 *======================================================*/
		function fn_selectRow() {
		 
		   var v_checked = this.checked,
		   v_row = $(this).closest("tr"),
		   v_grid = $("#gridMain").data("kendoGrid"),
		   v_dataItem = v_grid.dataItem(v_row);
		   
			g_centerCd    = v_dataItem.centerCd;
		    g_shipperCd   = v_dataItem.shipperCd;
		    g_inboundDate = v_dataItem.inboundDate;
		    g_inboundNo   = v_dataItem.inboundNo;
		    
		   if (!fn_gridControl("checkbox", v_row)) {
			   this.checked = false;
			   g_checkedMasterIds[v_dataItem.id] = false;
			   return;
		   }
		   
			var v_raw = g_mainDataSource.data();
		    var v_view = g_mainDataSource.view();
		    var v_length = v_raw.length;
		    var v_item, i;
		    
		    for (i = v_length - 1; i >= 0; i--) {
		    	v_item = v_raw[i];		
		    	if (!g_checkedMasterIds[v_item.id]) {
		    		v_grid.tbody.find("tr[data-uid='"+ v_item.uid + "']").removeClass("k-state-selected");
		    	
		    	} else {
		    		if(v_dataItem.uid != v_item.uid) {
		    			v_grid.tbody.find("tr[data-uid='"+ v_item.uid + "']").removeClass("k-state-selected");
		    		}
		    	} 
		    }
		    
		   g_checkedMasterIds[v_dataItem.id] = v_checked;
		   if (v_checked) {
			   v_row.addClass("k-state-selected");	
		   } 
		   
		   	var v_gridSelectItem = v_grid.dataItem(v_grid.select())
			if (v_gridSelectItem == null) {
				v_row.addClass("k-state-selected");	
			} 	 
		}
		
	
	
	
	/*======================================================*
	 * - Function 명   : fn_selectSlaveRow                  *
	 * - Function 기능 : Slave 체크박스 클릭/해제 처리      *   
	 *======================================================*/
	function fn_selectSlaveRow() {
		console.log('### fn_selectSlaveRow in ');
		// 
		
		debugger;
	   	var v_checked = this.checked,
	   	v_row = $(this).closest("tr"),
	   	v_grid = $("#gridSlave").data("kendoGrid"),
	   	v_dataItem = v_grid.dataItem(v_row);
	   	
	   	if (!fn_secGridControl("checkbox", v_row)) {
		   	this.checked = false;
		   	g_checkedSlaveIds[v_dataItem.id] = false;
		   	return;
	   	}
	   	
		var v_raw = g_slaveDataSource.data();
	    var v_view = g_slaveDataSource.view();
	    var v_length = v_raw.length;
	    var v_item, i;
	    
	    for (i = v_length - 1; i >= 0; i--) {
	    	v_item = v_raw[i];		
	    	if (!g_checkedSlaveIds[v_item.id]) {
	    		v_grid.tbody.find("tr[data-uid='"+ v_item.uid + "']").removeClass("k-state-selected");
	    	} else {
	    		if(v_dataItem.uid != v_item.uid) {
	    			v_grid.tbody.find("tr[data-uid='"+ v_item.uid + "']").removeClass("k-state-selected");
	    		}
	    	} 
	    }	 
	    
	   	g_checkedSlaveIds[v_dataItem.id] = v_checked;
	   	if (v_checked) {
		   	v_row.addClass("k-state-selected");		
	   	}
	   	
	   	var v_gridSelectItem = v_grid.dataItem(v_grid.select())
		if (v_gridSelectItem == null) {
			v_row.addClass("k-state-selected");	
		} 	
	}
	
	/*======================================================*
	 * - Function 명   : fn_validation                *
	 * - Function 기능 : 체크박스 클릭/해제 처리            *
	 * - param : gridFlag master OR slave 구분값            *
	 * - param : item validation 체크할 그리드 Row          *
	 *======================================================*/
	function fn_validation(gridFlag, item) {
		if (gridFlag == "master") {
			//마스터 그리드 validation
			
			if (item.inboundDivNm == '' || item.inboundDivNm == null) {
		    	fn_confirmPopupOpen("입고일자를 입력하여 주십시오.", "입고대기");
		    	return false;
			}	
			
			if (item.inboundDivNm == '' || item.inboundDivNm == null) {
		    	fn_confirmPopupOpen("입고구분을 입력하여 주십시오.", "입고대기");
		    	return false;
			}
			
			if (item.supplyCd == '' || item.supplyCd == null) {
		    	fn_confirmPopupOpen("공급처코드를 입력하여 주십시오.", "입고대기");
		    	return false;
			}

		} else {
			//Slave 그리드 validation
			if (item.prodCd == '' || item.prodCd == null) {
		    	fn_confirmPopupOpen("상품코드를 입력하여 주십시오.", "입고대기");
		    	return false;
			}
			
			if (item.prodStatusNm == '' || item.prodStatusNm == null) {
		    	fn_confirmPopupOpen("상품상태를 입력하여 주십시오.", "입고대기");
		    	return false;
			}
			
			if (item.lot == '' || item.lot == null) {
		    	fn_confirmPopupOpen("LOT를 입력하여 주십시오.", "입고대기");
		    	return false;
			}		
			
			if (item.excQty == '' || item.excQty == null) {
		    	fn_confirmPopupOpen("입고수량을 입력하여 주십시오.", "입고대기");
		    	return false;
			}					
		}
		return true;
	}
	
	/*===================================================================*
	 * - Function 명   : fn_slaveGridCreateRowCheck                      *
	 * - Function 기능 : 슬레이브 그리드 신규 버튼 클릭시 필수 값을 체크 * 
	 *                   필수 값 미존재 시 추가로 신규 레코드 생성 불가  *                                              
	 *===================================================================*/	
	function fn_slaveGridCreateRowCheck() {
 		var v_sRaw  = g_slaveDataSource.data();
 		var v_ssRaw = g_slaveDataSource.data();
	    var v_sLength = v_sRaw.length;
	    var v_sItem, v_sUid, i;
	    var v_ssItem, v_ssUid, j;
	    
	    for(i = v_sLength - 1; i >= 0; i--) {
	    	v_sItem = v_sRaw[i];
	    	
	    	if (v_sItem.crud == "C") {
	    		v_sUid = v_sItem.uid;
	    		
	    		for(j = v_sLength -1; j >=0; j--) {
	    			v_ssItem = v_ssRaw[j];
	    			
	    			if (v_ssItem.crud == "C") {
	    				if (v_sUid != v_ssUid) {
	    					if (!fn_validation("slave", v_ssItem)) {
	    						return false;
	    					}
	    				} 
	    			}
	    		}
	    	} else {
	    		return true;
	    	}
	    }
	    return true;
	}
	
	/*===============================================================*
	 * - Function 명   : fn_btnSearchPnlClick                        *
	 * - Function 기능 : 검색패널 검색 버튼 클릭에 대한 처리         * 
	 * - param : callFlag 검색버튼 클릭/CUD호출을 구분하기 위한 값   *
	 *           Validation 체크를 위해 사용                         *                                              
	 *===============================================================*/
	function fn_btnSearchPnlClick(callFlag) {
		
		var v_grid = $("#gridMain").data("kendoGrid");
		var v_sGrid = $("#gridSlave").data("kendoGrid");
		
		if (callFlag != "callback" && 
				(!fn_modifyDataCheck(g_mainDataSource, v_grid, "btnSearch", null) ||
				 !fn_modifyDataCheck(g_slaveDataSource, v_sGrid, "btnSearch", null))
				) {
			
 			$("#confirmDiv .message").empty().text("그리드에 신규생성 및 변경한 데이터가 존재합니다. \n검색을 진행하시겠습니까?");
			$("#confirmDiv").dialog({
				closeOnEscape: false,
				resizable : false,
				width : 320,
				modal : true,
				title : "입고대기",
				buttons : {
					Ok : {
						text : '확인',/*확인*/
						click : function() {
							$(this).dialog('close');
							g_srchMsgPrint = "Y";
							var v_params = {}

							getfilterClear(v_sGrid);
							getfilterClear(v_grid);

							v_params['centerCd']      = GLB_USER.centerCd;
							v_params['shipperCd']     = GLB_USER.shipperCd;
							v_params['inboundDiv']    = $('#ibdDivCd').val();
							v_params['inboundStatus'] = inboundStatus;
							v_params['inboundNo']     = $('#ibdNo').val();
							v_params['inboundStrDate']  = $('#ibdStrDt').val();
							v_params['inboundEndDate']  = $('#ibdEndDt').val();
							v_params['inboundPageFlag']  = 'inboundReady';	// 입고조회 쿼리를 공통으로 사용하기위한 구분자 추가 20170105.김범수
							g_srchParams = v_params;

							g_mainDataSource.read();	
							$(".right_slide").css("display","none");
							$("#top-pane").trigger("click");
							$('a.k-grid-filter').removeClass('k-state-active');
						}
					},
					Cancel : {
						text : '취소',/*확인*/
						click : function() {
							$(this).dialog('close');
							return;
						}
					},
				}
			});	 			
 		} else {
 			g_srchMsgPrint = "Y";
			var v_params = {};
			

			getfilterClear(v_sGrid);
			getfilterClear(v_grid);

			$('a.k-grid-filter').removeClass('k-state-active');
			v_params['centerCd']      = GLB_USER.centerCd;
			v_params['shipperCd']     = GLB_USER.shipperCd;
			v_params['inboundDiv']    = $('#ibdDivCd').val();
			v_params['inboundStatus'] = inboundStatus;
			v_params['inboundNo']     = $('#ibdNo').val();
			v_params['inboundStrDate']  = $('#ibdStrDt').val();
			v_params['inboundEndDate']  = $('#ibdEndDt').val();
			v_params['inboundPageFlag']  = 'inboundReady';	// 입고조회 쿼리를 공통으로 사용하기위한 구분자 추가 20170105.김범수	
			
			g_test += 1;
			console.log(g_test + "= CENTER CD  : " + v_params.centerCd);
			console.log(g_test + "= SHIPPER CD : " + v_params.shipperCd);
			
			g_srchParams = v_params;

			g_mainDataSource.read();	
			$(".right_slide").css("display","none");
			$("#top-pane").trigger("click");
 		}	
		searchBoxEvent();
	}
	
	/*======================================================*
	 * - Function 명   : fn_srchPnlInit                     *
	 * - Function 기능 : 검색패널 초기화 버튼 클릭          *   
	 *======================================================*/
	function fn_srchPnlInit() {
		$('#ibdNo').val('');
		$('#csrCd').val('');
		$('#ibdStrDt, #ibdEndDt').val('');	
		$('#lgtCenterCd').val('');
		$('#csrCd').val(''); 
		$('#ibdDivCd').val(''); 
		// $('#statusCd').val('');	// 사용안함 20170105. 김범수
	}
	
	/*========================================================================== *
	 * - Function 명   : fn_isEditable                                           *
	 * - Function 기능 : edit 이벤트 발생 시 CELL EDIT 활성화/비활성화 처리      * 
	 * - param : fieldName 그리드 컬럼명(edit 활성화 할 컬럼)                    *
	 * - param : model 이벤트 model                                              *
	 *========================================================================== */	
	function fn_isEditable(fieldName, model)  {
		 
		if (model.crud != "C") {
			if (fieldName == "inboundDate") {
				return false;
			}
		}
		
		 /* 임시추가 */
		if (fieldName == "supplyCd" || fieldName == "prodCd") {
			return false;
		}
		/* 임시추가 */
		 
		if (fieldName == "supplyNm") {
			return false;
		}

		if (fieldName.toLowerCase() == "shelfLifeDIvNm".toLowerCase()) {
			return false;
		}
		if (fieldName == "prodNm" || fieldName == "prodPltQty" || fieldName == "prodPkgQty" || fieldName == "prodSizeWidth" 
				|| fieldName == "prodSizeDepth" || fieldName == "prodSizeHeight" || fieldName == "prodWeigth" 
				|| fieldName == "prodVol" || fieldName == "storageDivNm" || fieldName == "inboundPltQty" || fieldName == "inboundPkgQty" 
				|| fieldName == "inboundEaQty" || fieldName == "excPltQty" || fieldName == "excPkgQty" || fieldName == "excEaQty" 
		
		) {
			return false;
		}
		
		if (fieldName == "excQty") {
			var v_sGrid = $("#gridSlave").data("kendoGrid");
			var v_sDataItem = v_sGrid.dataItem(v_sGrid.select());
			
			if (v_sDataItem.prodCd == null || v_sDataItem.prodCd =='') {
				return false;
			}			
		}
		
	    return true;
	}
	
	/*========================================================================== *
	 * - Function 명   : fn_gridControl                                          *
	 * - Function 기능 : 그리드 동작 처리                                        *
	 *                   마스터 그리드 change 이벤트 발생 시 처리                * 
	 *========================================================================== */	
	function fn_gridControl(callFunc, checkboxCheckRow) {
		var v_gridChangeFlag = true;
		var v_sParamCheckedIds = {};
		var v_dataItem;
		v_sParamCheckedIds     = g_checkedSlaveIds;
		var v_grid     = $("#gridMain").data("kendoGrid");
		if (!fn_modifyDataCheck(g_mainDataSource, v_grid, callFunc, checkboxCheckRow)) {
	    	fn_confirmPopupOpen("그리드에 신규생성 데이터가 존재합니다. \n이전 추가작업을 완료하십시오.", "입고대기");
	        return false;
		}
		if (checkboxCheckRow == null) {
			var v_dataItem = v_grid.dataItem(v_grid.select());	
			
			g_centerCd    = v_dataItem.centerCd;
		    g_shipperCd   = v_dataItem.shipperCd;
		    g_inboundDate = v_dataItem.inboundDate;
		    g_inboundNo   = v_dataItem.inboundNo;
		    
			fn_paging($("#gridMain"), g_mainDataSource);
		} else {
			var v_dataItem = v_grid.dataItem(checkboxCheckRow);
			fn_paging_checkbox($("#gridMain"), v_dataItem, g_mainDataSource);
		}
		
		var v_sRaw    = g_slaveDataSource.data();
		var v_sView   = g_slaveDataSource.view();
		var v_sLength = v_sRaw.length;
		var v_sItem, i;	
		for (i = v_sLength - 1; i >= 0; i--) {	// 슬레이브 그리드 레코드 수 만큼 LOOP
			v_sItem = v_sRaw[i];
			if (v_sItem.dirty || v_sItem.crud=="C") {	// 슬레이브 그리드 체크박스 체크 값(수정된 레코드)이 있다면,
				if (g_masterUid == null || v_dataItem == null || g_masterUid == v_dataItem.uid) {
					v_gridChangeFlag = false;
					
				} else {
					if (v_sItem.dirty) {
						v_gridChangeFlag = true;	// 메시지 박스 출력여부 : TRUE
					}
					
					if (v_sItem.crud=="D") {
						v_gridChangeFlag = false;
					}
					
					if (v_sParamCheckedIds[v_sItem.id] && v_sItem.crud=="C") {
						v_gridChangeFlag = true;
					} 
				}

				if (v_gridChangeFlag && g_srchMsgPrint == "N") {	 		// 메시지 박스 출력여부 TRUE라면..
					$("#loading").hide();
					$("#confirmDiv .message").empty().text("상세정보에 신규생성 및 변경한 데이터가 존재합니다. \n변경 데이터를 무시하고 검색을 진행하시겠습니까?");
					$("#confirmDiv").dialog({
						closeOnEscape: false,
						resizable : false,
						width : 320,
						modal : true,
						title : "입고대기",
						buttons : {
							Ok : {
								text : '확인',				// 확인버튼
								click : function() {
									$(this).dialog('close');
									$("#loading").show();
									v_gridChangeFlag = true;		
									g_checkedSlaveIds = {};
									g_slaveGridCallFalg = true;
									fn_slaveGridsrch(v_dataItem.centerCd, v_dataItem.shipperCd, v_dataItem.inboundNo, v_dataItem.inboundDate, null);	// 확인 시 슬레이브 그리드 조회
									return true; 						// 확인 버튼 클릭시 여기서 끝!
								}
							},
							Cancel : {
								text : '취소',				// 취소버튼
								click : function() {
									$(this).dialog('close');
									v_gridChangeFlag = false;
									g_slaveGridCallFalg = false;	// 슬레이브 그리드 조회값 : FALSE
									if (g_masterRow != null) {		
										v_grid.select(g_masterRow);	// 취소 시 기존 마스터 레코드 SELECT
									} else {
										v_grid.select(v_grid.tbody.find(">tr:first"));
									}
									
									if (checkboxCheckRow != null) {
										checkboxCheckRow.find(".ChkBoxMainGrid").prop("checked",false);
									}
									
									return false;					// 취소 버튼 클릭시 여기서 끝!
								}
							},
						}
					});
					return true;									//메시지 박스 출력하고 끝!						
				} 
			}
		}
		
		if (v_dataItem != null && v_dataItem.uid != g_masterUid) {		//슬레이브 그리드 조회
			if (v_dataItem.crud == "C") {
				fn_slaveGridsrch("test", "test", null, null, "init");
				g_slaveGridCallFalg = true;
				g_masterUid = v_dataItem.uid;				
			} else {
				fn_slaveGridsrch(v_dataItem.centerCd, v_dataItem.shipperCd, v_dataItem.inboundNo, v_dataItem.inboundDate, null);
				g_slaveGridCallFalg = true;
				g_masterUid = v_dataItem.uid;
			}
		}
		return true;
	}
	
	/*========================================================================== *
	 * - Function 명   : fn_secGridControl                                       *
	 * - Function 기능 : 두번째 그리드 동작 처리                                 *
	 *                   슬레이브 그리드 change 이벤트 발생 시 처리              * 
	 *========================================================================== */	
	function fn_secGridControl(callFunc, checkboxCheckRow) {
		console.log('### fn_secGridControl in');
		 
		var v_gridChangeFlag = true;
		var v_dataItem;
		var v_grid     = $("#gridSlave").data("kendoGrid");
		
		if (checkboxCheckRow == null) {
			v_dataItem = v_grid.dataItem(v_grid.select());	
			fn_paging($("#gridSlave"), g_slaveDataSource);
		} else {
			v_dataItem = v_grid.dataItem(checkboxCheckRow);
			fn_paging_checkbox($("#gridSlave"), v_dataItem, g_slaveDataSource);
		}
		
		return true;
	}	

	/* 장원봉 20161229 CommonCode code 조회 */
	/*========================================================= *
	 * - Function 명   : fn_getCommonCode                       *
	 * - Function 기능 : 그리드에서 입력한 공통코드 Name으로    *
	 *                   공통코드 code값을 뽑아온다.            *
	 *                   Kendo의 dropDownList를 사용하여 코드를 *
	 *                   매핑하는 방법을 찾지 못하여 구현함.    *
	 * - param : array 공통코드를 담고 있는 array변수           *
	 * - param : codeName 공통코드에 매핑되어 있는 Name을       *
	             찾기위해 비교에 사용할 변수                    *
	 *========================================================= */
	function fn_getCommonCode(array, codeName) {

		 for (var k = 0; k < array.length; k++) {
			var v_inputName = array[k].name;
			if (v_inputName == codeName) {
				return array[k].id;
			}
		}
	}
	
	/* 장원봉 20161229 팝업처리 */
	/*======================================================*
	 * - Function 명   : fn_setChildValue                   *
	 * - Function 기능 : 팝업 전달 값 받아오기              *
	 *======================================================*/
	function fn_setChildValue(param) {
		console.log('### fn_setChildValue in');
		var v_flag = param.flag;
		if (v_flag == "supply") {
			var v_grid = $("#gridMain").data("kendoGrid");
			var dataItem = v_grid.dataItem(g_currentRow);
			console.log(dataItem.uid);
			if (param.supplyCd == null) {
				dataItem.set("supplyCd", null);
				dataItem.set("supplyNm", null);
				dataItem.dirty = false;
				/* 사용안함 20170329.김범수
				v_grid.select().each(function(i) {
					var v_trChildren = $(this).children('td[role="gridcell"]');
					var v_td  = v_trChildren.eq(4);
					var v_td1 = v_trChildren.eq(5);
					
					v_td.removeClass("k-dirty-cell");
					v_td.empty();
					
					v_td1.removeClass("k-dirty-cell");
					v_td1.empty();
				});
				 */
			} else {
				dataItem.dirty = true;
				dataItem.set("supplyCd", param.supplyCd);
				dataItem.set("supplyNm", param.supplyNm);
	
				// 팝업을 통해서 바인딩 되는 경우 현재 Row를 가져오는 부분 수정 / 팝업 OR 팝업이 아닌경우로 나뉨 20170327.김범수
				var selectionRow = v_grid.tbody.find(">tr:first");
				var v_raw = g_mainDataSource.data();
				var v_length = v_raw.length;
			    var v_item, i;
			    for (i = v_length - 1; i >= 0; i--) {	// 체크박스 선택된 row들이 모두 selection 되는현상 제거
			    	v_item = v_raw[i];		
			    	v_grid.tbody.find("tr[data-uid='"+ v_item.uid + "']").removeClass("k-state-selected");
			    }	 
			    
				if(null == g_currentRow){	// 현재 작업중인 row만 selection 
					v_grid.select(selectionRow);
				}else{
					v_grid.tbody.find("tr[data-uid='"+ g_masterUid + "']").addClass("k-state-selected");
				}
				
				/* 사용안함 20170329.김범수
				v_grid.select().each(
						function(i) {
							var v_trChildren = $(this).children('td[role="gridcell"]');
							var v_td  = v_trChildren.eq(4);
							var v_td1 = v_trChildren.eq(5);
							var v_uid = v_grid.tbody.find("tr[data-uid='" + dataItem.uid + "']");
	
							v_td.addClass("k-dirty-cell");
							v_td.prepend("<span class='k-dirty'></span>");
							
							v_td1.addClass("k-dirty-cell");
							v_td1.prepend("<span class='k-dirty'></span>");
							
							g_currentRow.find(".ChkBoxMainGrid").prop("checked", "checked");
							g_checkedMasterIds[dataItem.id] = true;
						});
				*/
			}
		}
		else {
			var v_sGrid = $("#gridSlave").data("kendoGrid");
			var v_sDataItem = v_sGrid.dataItem(v_sGrid.select());
			
			if (param.prodCd == null) {
				v_sDataItem.set("prodCd", null);
				v_sDataItem.set("prodNm", null);
				
				v_sDataItem.set("prodPltQty", null);
				v_sDataItem.set("prodPkgQty", null);
				v_sDataItem.set("prodSizeWidth", null);
				v_sDataItem.set("prodSizeDepth", null);
				v_sDataItem.set("prodSizeHeight", null);
				v_sDataItem.set("prodWeigth", null);
				v_sDataItem.set("prodVol", null);
				v_sDataItem.set("shelfLifeDIv", null);
				v_sDataItem.set("shelfLifeDivNm", null);
				v_sDataItem.set("storageDiv", null);
				v_sDataItem.set("storageDivNm", null);
				
				v_sDataItem.dirty = false;
				/* 현재 사용하지 않아서 주석처리 20170327.김범수
				v_sGrid.select().each(function(i) {
					
					var v_sTrChildren = $(this).children('td[role="gridcell"]');
					var v_sTd  = v_sTrChildren.eq(3);
					var v_sTd1 = v_sTrChildren.eq(4);
					v_sTd.removeClass("k-dirty-cell");
					v_sTd.empty();
					
					v_sTd1.removeClass("k-dirty-cell");
					v_sTd1.empty();
				});
				*/
			} else {
				// 
				// 팝업을 통해서 바인딩 되는 경우 현재 Row를 가져오는 부분 수정 / 팝업 OR 팝업이 아닌경우로 나뉨 20170327.김범수
				g_popupCurrentRow = v_sGrid.tbody.find("tr[data-uid='" + v_sDataItem.uid + "']");
				g_popupCurrentUid = v_sDataItem.uid;
				// end 20170327.김범수
				
				v_sDataItem.dirty = true;
				v_sDataItem.set("prodCd", param.prodCd);
				v_sDataItem.set("prodNm", param.prodNm);
				
				v_sDataItem.set("prodPltQty", param.prodPltQty);
				v_sDataItem.set("prodPkgQty", param.prodPkgQty);
				v_sDataItem.set("prodSizeWidth", param.prodSizeWidth);
				v_sDataItem.set("prodSizeDepth", param.prodSizeDepth);
				v_sDataItem.set("prodSizeHeight", param.prodSizeHeight);
				v_sDataItem.set("prodWeigth", param.prodWeigth);
				v_sDataItem.set("prodVol", param.prodVol);
				v_sDataItem.set("shelfLifeDIv", param.shelfLifeDIv);
				v_sDataItem.set("shelfLifeDivNm", param.shelfLifeDivNm);
				v_sDataItem.set("storageDiv", param.storageDiv);
				v_sDataItem.set("storageDivNm", param.storageDivNm);
				
				/*
				입고대기에서는 이 부분에서 체크박스 처리로직이 필요없음 20170329.김범수
				*/
				
				g_popupCurrentRow = null;	// 팝업바인딩 데이터 초기화 20170327.김범수
				
				/* 현재 사용하지 않아서 주석처리 20170327.김범수
				v_sGrid.select().each(function(i) {
					
					var v_sTrChildren = $(this).children('td[role="gridcell"]');
					var v_sTd  = v_sTrChildren.eq(3);
					var v_sTd1 = v_sTrChildren.eq(4);
					var v_sUid = v_sGrid.tbody.find("tr[data-uid='" + v_sDataItem.uid + "']");
	
					v_sTd.addClass("k-dirty-cell");
					v_sTd.prepend("<span class='k-dirty'></span>");
					
					v_sTd1.addClass("k-dirty-cell");
					v_sTd1.prepend("<span class='k-dirty'></span>");
					
					var v_sTd2 = v_sTrChildren.eq(23);		//불필요한 dirty가 생성되어 인위적으로 지워줌.
					v_sTd2.children('span').remove();
					
					v_sUid.find(".ChkBoxMainGrid").prop("checked", "checked");
					g_checkedSlaveIds[v_sDataItem.id] = true;
					
				});
				 */
			}		
			 
		}
		
		g_supplyPopYn = "N";
	}

	/*======================================================*
	 * - Function 명   : fn_popupClose                      *
	 * - Function 기능 : Popup Close                        *
	 *======================================================*/
	function fn_closePopup() {
		 
		$("#loading").hide();
		$(".k-overlay").hide();
		$(".k-window").each(function(index) {
			$(this).hide();
		});
	}
	
	/*======================================================*
	 * - Function 명   : fn_closePopupCheck                  *
	 *======================================================*/	
	function fn_closePopupCheck() {
				
	}

	/*======================================================*
	 * - Function 명   : fn_openPopup                       *
	 * - Function 기능 : Popup Open                         *
	 * - param 그리드 DIV(jquery selector)                  *
	 * - param controller 호출 URL(init url)                *
	 * - param 팝업 타이틀                                  *
	 *======================================================*/
    function fn_openPopup(griddiv, url, title, width, height) {
		$(griddiv).kendoWindow({
			actions : [ "Close" ],
			content : url,
			draggable : false,
			width : width,
			height : height,
			modal : true,
			pinned : false,
			resizable : false,
			title : title,
			visible : false,
			iframe : true,
			type : "GET",
			animation : false,
			close: function() {
				fn_closePopup();
            }
		});

		$(griddiv).data("kendoWindow").open().center();
	}

	/*=======================================================*
	 * - Function 명   : fn_checkboxCheck                    *
	 * - Function 기능 : 그리드 체크박스 체크여부 체크       *
	 * - param checkIds 체크박스 전역변수(마스터 or 슬레이브 *
	 * - param datasource(master or slave)                   *
	 *=======================================================*/
	function fn_checkboxCheck(checkIds, dataSource) {
		var v_raw = dataSource.data();
		var v_view = dataSource.view();
		var v_length = v_raw.length;
		var v_item, i;

		for (i = v_length - 1; i >= 0; i--) {
			v_item = v_raw[i];
			if (checkIds[v_item.id]) {
				return true;
			}
		}
		return false;
	}

	/*=======================================================*
	 * - Function 명   : fn_dirtyCheck                       *
	 * - Function 기능 : 그리드 수정여부                     *
	 * - param datasource(master or slave)                   *
	 *=======================================================*/
	function fn_dirtyCheck(dataSource) {
		var v_raw = dataSource.data();
		var v_view = dataSource.view();
		var v_length = v_raw.length;
		var v_item, i;

		for (i = v_length - 1; i >= 0; i--) {
			v_item = v_raw[i];
			if (v_item.dirty) {
				return true;
			}
			
			if (v_item.crud == "C") {
				return true;
			}
		}
		return false;
	}

	/*======================================================*
	 * - Function 명   : fn_modifyDataCheck                 *
	 * - Function 기능 : 그리드 수정 사항 체크              *
	 * - param datasource(master or slave)                  *
	 * - param grid(master or slave)                        *
	 * - param callFunc 호출한 Function명(구분자)           *
	 *======================================================*/
	function fn_modifyDataCheck(dataSource, grid, callFunc, checkboxCheckRow) {
		var v_raw = dataSource.data();
		var v_view = dataSource.view();
		var v_length = v_raw.length;
		var v_item, i;

		if (callFunc == "change" && grid == $("#gridMain").data("kendoGrid")) {
			var v_dataItem = grid.dataItem(grid.select());
			if (v_dataItem != null) {
				var v_currentUid = v_dataItem.uid;

				for (i = v_length - 1; i >= 0; i--) {
					v_item = v_raw[i];
					if (v_item.crud == "C") {
						var v_beforeUid = v_item.uid;
						var v_befRow = grid.tbody.find("tr[data-uid='"+ v_beforeUid + "']");
						if (v_currentUid != v_beforeUid) {
							grid.select(v_befRow);
							return false;
						}
					}
				}
			}
		} else if (callFunc == "checkbox" && grid == $("#gridMain").data("kendoGrid")) {

			var v_dataItem = grid.dataItem(checkboxCheckRow);
			if (v_dataItem != null) {
				var v_currentUid = v_dataItem.uid;
				for (i = v_length - 1; i >= 0; i--) {
					v_item = v_raw[i];
					if (v_item.crud == "C") {
						var v_beforeUid = v_item.uid;
						var v_befRow = grid.tbody.find("tr[data-uid='"	+ v_beforeUid + "']");
						if (v_currentUid != v_beforeUid) {
							grid.select(v_befRow);
							return false;
						}
					}
				}
			}
		} else if (callFunc == "btnNew" && grid == $("#gridMain").data("kendoGrid")) {
			for (i = v_length - 1; i >= 0; i--) {
				v_item = v_raw[i];

				if (v_item.crud != null && v_item.crud == "C") {
					return false;
				}
			}
		} else if (callFunc == "btnDelete" && grid == $("#gridSlave").data("kendoGrid")) {
			
			for (i = v_length - 1; i >= 0; i--) {
				v_item = v_raw[i];
				
				if (v_item.crud == "C") {
					return true;
				}
				
				if (v_item.dirty) {
					return false;
				}
			}		
		
		} else {
			if (grid.tbody.find('.k-edit-cell').length) {
				return false;
			}
			for (i = v_length - 1; i >= 0; i--) {
				v_item = v_raw[i];
				if (v_item.dirty) {
					return false;
				}

				if (v_item.crud != null) {
					if (callFunc == "btnDelete") {
						return true;
					} else {
						return false;
					}
				}
			}
		}
		return true;
	}

	/*=============================================================*
	 * - Function 명   : fn_goMasterCheckAll                       *
	 * - Function 기능 : Master 그리드 전체선택 체크박스 클릭 처리 *   
	 *=============================================================*/
	function fn_goMasterCheckAll() {
		var v_raw = g_mainDataSource.data();
	    var v_view = g_mainDataSource.view();
	    var v_length = v_raw.length;
	    var v_item, i;
		    
		if ($("#checkMasterAll").is(':checked')) {
		    for (i = v_length - 1; i >= 0; i--) {							// 마스터 그리드 ROW 수 만큼 LOOP
		    	v_item = v_raw[i];
		    	g_checkedMasterIds[v_item.id] = true;
		    }			
			
			fn_checkboxAll("chkBox", true);
		} else {
		    for (i = v_length - 1; i >= 0; i--) {							// 마스터 그리드 ROW 수 만큼 LOOP
		    	v_item = v_raw[i];
		    	g_checkedMasterIds[v_item.id] = false;
		    }
		    
			fn_checkboxAll("chkBox", false);
		}
	}

	/*=============================================================*
	 * - Function 명   : fn_goSlaveCheckAll                        *
	 * - Function 기능 : Slave 그리드 전체선택 체크박스 클릭 처리  *   
	 *=============================================================*/
	function fn_goSlaveCheckAll() {
			var v_raw = g_slaveDataSource.data();
		    var v_view = g_slaveDataSource.view();
		    var v_length = v_raw.length;
		    var v_item, i;
		    
			if ($("#checkSlaveAll").is(':checked')) {
			    for (i = v_length - 1; i >= 0; i--) {							// 마스터 그리드 ROW 수 만큼 LOOP
			    	v_item = v_raw[i];
			    	g_checkedSlaveIds[v_item.id] = true;
			    }
				fn_checkboxAll("subChkBox", true);
				
			} else {
			    for (i = v_length - 1; i >= 0; i--) {							
			    	v_item = v_raw[i];
			    	g_checkedSlaveIds[v_item.id] = false;
			    }
			    
				fn_checkboxAll("subChkBox", false);
			}
	}

	/*=======================================================*
	 * - Function 명   : fn_checkboxAll                      *
	 * - Function 기능 : 체크박스 전체선택/전체해제          *  
	 * - param : checkboxName 체크박스 이름(Master or Slave) *
	 * - param : checkValue true or false                    *
	 *=======================================================*/
	function fn_checkboxAll(checkboxName, checkValue) {
		$("input:checkbox[name=" + checkboxName + "]").each(function() {
			$(this).prop("checked", checkValue);
		});
	}

	/*======================================================*
	 * - Function 명   : fn_slaveGridsrch                   *
	 * - Function 기능 : Slave 그리드 조회처리              *
	 * - param : centerCd 물류센터 코드                     *
	 * - param : csrCd 화주코드                             *
	 * - param : inboundNo 입고번호                         *
	 * - param : inboundDate 입고날짜                       *
	 *======================================================*/
	function fn_slaveGridsrch(centerCd, shipperCd, inboundNo, inboundDate, callFlag) {
		 
		var v_params = {};
		v_params['centerCd'] = centerCd;
		v_params['shipperCd'] = shipperCd;
		
		if (inboundDate != null) {
			v_params['inboundDate'] = inboundDate.yyyymmdd();	
		}
		
		v_params['inboundNo'] = inboundNo;
		v_params['inboundPageFlag']  = 'inboundReady';	// 입고조회 쿼리를 공통으로 사용하기위한 구분자 추가 20170105.김범수
		
		g_srchSlaveParams = v_params;
		g_slaveDataSource.read();
	}

	/*======================================================*
	 * - Function 명   : fn_confirmPopupOpen                *
	 * - Function 기능 : 확인메시지 팝업Open                *
	 * - param : message 팝업창에 나타날 메시지             *
	 * - param : title 팝업창의 타이틀                      *   
	 *======================================================*/
	function fn_confirmPopupOpen(message, title) {
		
		$("#confirmDiv .message").empty().html(message);
		$("#confirmDiv").dialog({
			closeOnEscape : false,
			resizable : false,
			width : 320,
			modal : true,
			title : title,
			buttons : {
				Ok : {
					text : '확인',/*확인*/
					click : function() {
						$(this).dialog('close');
					}
				}
			}
		});
	}

	/*=======================================================*
	 * - Function 명   : fn_masterDataBound                  *
	 * - Function 기능 : 마스터 그리드 데이터 바인딩 후 처리 *   
	 *=======================================================*/
	function fn_masterDataBound(e) {
		var v_grid = $("#gridMain").data("kendoGrid");
		var view = this.dataSource.view();
		for (var i = 0; i < view.length; i++) {
			if (g_checkedMasterIds[view[i].id]) {
				this.tbody.find("tr[data-uid='" + view[i].uid + "']").addClass(
						"k-state-selected").find(".ChkBoxMainGrid").attr(
						"checked", "checked");
			}
		}

		fn_paging($("#gridMain"), g_mainDataSource); //그리드 상단 COUNT 표기처리

		if (fn_modifyDataCheck(g_mainDataSource, v_grid, "dataBound", null)) {
			fn_lastSelectionRow(v_grid, this.dataSource.view(), g_lGridSelectSeq, "master");
		}
	}

	/*=======================================================*
	 * - Function 명   : fn_slaveDataBound                  *
	 * - Function 기능 : Slave 그리드 데이터 바인딩 후 처리 *   
	 *=======================================================*/
	function fn_slaveDataBound(e) {
		 
		 debugger;

		var view = this.dataSource.view();
		for (var i = 0; i < view.length; i++) {
			if (g_checkedSlaveIds[view[i].id]) {
				this.tbody.find("tr[data-uid='" + view[i].uid + "']").addClass(
						"k-state-selected").find(".ChkBoxMainGrid").attr(
						"checked", "checked");
			}
		}

		fn_paging($("#gridSlave"), g_slaveDataSource); //그리드 상단 COUNT 표기처리
		
		$("#loading").hide();
		var v_params = {};
		g_srchParams = v_params;
		
		var v_grid = $("#gridSlave").data("kendoGrid");
		fn_lastSelectionRow(v_grid, this.dataSource.view(), g_mGridSelectSeq, "slave1");		
	}
	
	/*==================================================================*
	 * - Function 명   : fn_lastSelectionRow                            *
	 * - Function 기능 : 업데이트 후 selection할 값을 가져온다          *   
	 * - param v_grid  : 셀렉션 처리할 그리드                           *
	 * - param view    : 셀렉션 처리할 그리드의 view 데이터             *
	 * - seqArr        : Update/insert 후 자바로부터 받은 gridSeq       *
	 * - boundFlag     : 데이터 바운드 플래그(그리드 별 구분위해)       *
	 *==================================================================*/	
	function fn_lastSelectionRow(v_grid, view, selectGridSeq, boundFlag) {
		console.log('### fn_lastSelectionRow in');
		
		var selectionRow = v_grid.tbody.find(">tr:first");
		 
		if(null != selectGridSeq){
			if (selectGridSeq > 0) {
	 			for (var i = 0; i < view.length; i++) {
	 				if (view[i].gridSeq == selectGridSeq) {
	 					selectionRow = v_grid.tbody.find("tr[data-uid='" + view[i].uid + "']").addClass("k-state-selected"); 	
	 					
	 			 		//C또는 U가 아닐경우를 위해 gridSeq 초기화
	 			 		if (boundFlag == "master") {
	 			 			g_lGridSelectSeq = -1;
	 			 			
	 			 		} else if (boundFlag == "slave1") {
	 			 			g_mGridSelectSeq = -1;
	 			 			
	 			 		} else {
	 			 			g_sGridSelectSeq = -1;
	 			 		}
	 				}
	 			}
			}
		}
		// 
		// 팝업을 통해서 바인딩 되는 경우 현재 Row를 가져오는 부분 수정 / 팝업 OR 팝업이 아닌경우로 나뉨 20170327.김범수
		var v_raw = g_slaveDataSource.data();
		var v_length = v_raw.length;
	    var v_item, i;
	    for (i = v_length - 1; i >= 0; i--) {	// 체크박스 선택된 row들이 모두 selection 되는현상 제거
	    	v_item = v_raw[i];		
	    	v_grid.tbody.find("tr[data-uid='"+ v_item.uid + "']").removeClass("k-state-selected");
	    }	 
	    
		if(null == g_popupCurrentRow){	// 현재 작업중인 row만 selection 
			v_grid.select(selectionRow);
		}else{
			v_grid.tbody.find("tr[data-uid='"+ g_popupCurrentUid + "']").addClass("k-state-selected");
		}
 		// end 20170327.김범수 
	}	
	
	/* 장원봉 20161229 입고구분 콤보박스 */
	/*
	function test111111(container, options) {
		var input = $("<input/>");
        input.attr("name", options.field);
        input.attr("placeholder", "(optional)");
        input.appendTo(container);
	}
	*/

	/* 장원봉 20161229 입고구분 콤보박스 */
	function inboundDivDropDownEditor(container, options) {
		
		// console.log('@#@!!!@@#1 ' + JSON.stringify(g_inboundDivCode));
		$('<input name="' + options.field + '"/>').appendTo(container)
				.kendoDropDownList({
					dataSource : {
						data : g_inboundDivCode
					},
					dataTextField : "name",
					dataValueField : "name"
				});
	}
	
	
	
	/* 장원봉 20161230 상품상태 콤보박스 */
	function prodStsDropDownEditor(container, options) {
		
		
		$('<input name="' + options.field + '"/>').appendTo(container)
				.kendoDropDownList({
					dataSource : {
						data : g_prodStsCode
					},
					dataTextField : "name",
					dataValueField : "name"
				});
	}
	
	/* 장원봉 20161230 유통기한구분 콤보박스 */
	function shelfLifeDivDropDownEditor(container, options) {
		$('<input name="' + options.field + '"/>').appendTo(container)
				.kendoDropDownList({
					dataSource : {
						data : g_shelfLifeDivCode
					},
					dataTextField : "name",
					dataValueField : "name"
				});
	}
		
	/* 장원봉 20161230 보관구분 콤보박스 */
	function storageDivDropDownEditor(container, options) {
		$('<input name="' + options.field + '"/>').appendTo(container)
				.kendoDropDownList({
					dataSource : {
						data : g_storageDivCode
					},
					dataTextField : "name",
					dataValueField : "name"
				});
	}
	
	/* 장원봉 20170302 실행수량 양수만입력되도록 처리 */
	function onlyPositiveNum(container, options) {
	    $('<input/>')
	        .appendTo(container)
	        .kendoNumericTextBox({
	            min: 0
	        });
	}
	/*======================================================*
	 * - Function 명   : fn_masterGrid                      *
	 * - Function 기능 : 마스터 그리드처리                  *   
	 *======================================================*/
	function fn_masterGrid() {
			g_mainDataSource = new kendo.data.DataSource({
				batch : true,
				//pageSize : 50,
		 	   	transport: {
					read : function(options) {
						$.ajax({
							async : g_async,
							url : "/wms/inbound/selectInboundReadyMst.do",
							dataType : "jsonp",
					        beforeSend : function(request){
					        	request.setRequestHeader("guserid", GLB_USER.userid); 
					        	request.setRequestHeader("gdbinfo", GLB_USER.dbinfo);
					        	request.setRequestHeader("guserkey", GLB_USER.userkey);
					        },
					        
							data : g_srchParams,
							success : function(result) {
								options.success(result);
								fn_checkboxAll("chkBox", false);
								$("#checkMasterAll").prop("checked", false);
								g_checkedMasterIds = {};	// 마스터 그리드 체크박스 전역변수 초기화
								g_masterUid        = null;	// 초기화	
								g_srchParams = {};
								
								if (result.data && result.data.length == 0) {
									fn_slaveGridsrch("test", "test", null, null, "init");
								} 

							},
							error : function(x, o, e) {
								showExceptionError(x, o, e);
							}
						});
					},
					update: function (options) {
						$("#loading").show();
						$.ajax({
							url : "/wms/inbound/updateInbound.do",
							dataType : "jsonp",
					        beforeSend : function(request){
					        	request.setRequestHeader("guserid", GLB_USER.userid);
					        	request.setRequestHeader("gdbinfo", GLB_USER.dbinfo);
					        	request.setRequestHeader("gCenterCd", GLB_USER.centerCd); 
					        	request.setRequestHeader("gShipperCd", GLB_USER.shipperCd);
					        	request.setRequestHeader("gToDate", GLB_USER.shipperCd);
					        	request.setRequestHeader("guserkey", GLB_USER.userkey);
					        },
					        data : {
						        	masterModels : kendo.stringify(g_paramList),		//master grid data 
						        	slaveModels  : kendo.stringify(g_slaveParamList)	//slave grid data
					        },
							success : function(result) {
								if (result.result != "0000") {
							    	fn_confirmPopupOpen(result.result_mesg, "입고대기");
							    	$("#loading").hide();
							    	return;
							    	
								} else {		
									if (result.rtnGridSeq != null) { 
										g_lGridSelectSeq = result.rtnGridSeq.lGridSeq;
										g_mGridSelectSeq = result.rtnGridSeq.mGridSeq;
										g_sGridSelectSeq = result.rtnGridSeq.sGridSeq;
									}		
									if (result.result_mesg != null) {
										$("#loading").hide();
										$("#confirmDiv .message").empty().html(result.result_mesg);
										$("#confirmDiv").dialog({
											closeOnEscape: false,
											resizable : false,
											width : 320,
											modal : true,
											title : "입고대기",
											buttons : {
												Ok : {
													text : '확인',/*확인*/
													click : function() {
														$(this).dialog('close');
														fn_btnSearchPnlClick("callback");
														g_checkedMasterIds = {};	// 마스터 그리드 체크박스 전역변수 초기화
														g_checkedSlaveIds  = {};	// 슬레이브 그리드 체크박스 전역변수 초기화	
														g_masterUid        = null;	// 초기화
														
																					
													}
												}
											}
										});
									}
								}
							},
							error : function(x, o, e) {
								showExceptionError(x, o, e);
							}	
						});						
					},
				    destroy: function (options) {
				    	$("#loading").show();
						$.ajax({
							url : "/wms/inbound/deleteInbound.do",
							dataType : "jsonp",
					        beforeSend : function(request){
					        	request.setRequestHeader("guserid", GLB_USER.userid); 
					        	request.setRequestHeader("gdbinfo", GLB_USER.dbinfo);
					        	request.setRequestHeader("guserkey", GLB_USER.userkey);
					        },
					        data : {
						        	masterModels : kendo.stringify(g_paramList),		//master grid data 
						        	slaveModels  : kendo.stringify(g_slaveParamList)	//slave grid data
				        	},
							success : function(result) {
								if (result.result != "0000") {
									fn_confirmPopupOpen(result.result_mesg, "입고대기");
							    	$("#loading").hide();
							    	return;
							    	
							    	
								} else {			
									if (result.rtnGridSeq != null) {
										g_lGridSelectSeq = result.rtnGridSeq.lGridSeq;
										g_mGridSelectSeq = result.rtnGridSeq.mGridSeq;
										g_sGridSelectSeq = result.rtnGridSeq.sGridSeq;
									}	
									if (result.result_mesg != null) {			
										$("#confirmDiv .message").empty().html(result.result_mesg);
										$("#confirmDiv").dialog({
											closeOnEscape: false,
											resizable : false,
											width : 320,
											modal : true,
											title : "입고대기",
											buttons : {
												Ok : {
													text : '확인',/*확인*/
													click : function() {
														
														$(this).dialog('close');
														$("#loading").hide();
														g_checkedMasterIds = {};	// 마스터 그리드 체크박스 전역변수 초기화
														g_checkedSlaveIds  = {};	// 슬레이브 그리드 체크박스 전역변수 초기화
														g_masterUid        = null;  // 초기화
														fn_btnSearchPnlClick("callback");
														

													}
												}
											}
										});
									}
								}
							},
							error : function(x, o, e) {
								showExceptionError(x, o, e);
							}	
						});					    	
				    }
			},
			requestEnd: function(e) {
			},
			edit: function(e) {
			},
			change: function(e) {
				

				var selectedRowIndex =	 $('#gridMain').data("kendoGrid").select().index();
				
				if (e.items.length == 0) {
					fn_slaveGridsrch("test", "test", null, null, "init");
				}
				
				if (e.field != null && e.action == "itemchange") {
					var v_grid = $("#gridMain").data("kendoGrid");
					var v_dataItem = v_grid.dataItem(v_grid.select());
					
					var v_currentRow = v_grid.tbody.find("tr[data-uid='" + v_dataItem.uid + "']");
					
					
					console.log("e.field!!!!!!" + e.field);
					if (e.field == "inboundDate" ) {
					$("#gridMain").find('.k-grid-content tr:eq('+ selectedRowIndex +') td:eq(3)').click();
					} 
					
					
					/* 현재 사용하지 않아서 주석처리 20170327.김범수
 					if (e.field == "supplyCd" && g_supplyPopYn == "N") {
 						
 						g_supplyPopYn = "N";
		            	var param = {};
		            	param['callFlag'] = 'keyup';			// 쿼리 분기를 위해..
		            	param['shipperCd'] = GLB_USER.shipperCd;		// 화주
		            	param['supplyCd'] = v_dataItem.supplyCd;		// 공급처 코드
		            	fn_setChildValue() 해당 펑션 정상기능을 방해함에 따른 주석처리 20170325.김범수
		            	fn_getSupplyCd(param); 
					}    
					*/
					v_currentRow.find(".ChkBoxMainGrid").prop("checked","checked"); 
	        		g_checkedMasterIds[v_dataItem.id] = true;	
				}
			},
			schema : {
				data : "data",
				model : {
					id : "gridSeq",
					fields : {
						chkbox          : {type : "boolean"},
						inboundDate     : {type : "date"  , format: "{0:yyyy-MM-dd}", editable: true},
						inboundNo       : {type : "string", editable: false},
						inboundDiv      : {type : "string", editable: false},
						inboundDivNm    : {},
						supplyCd        : {type : "string", editable: true}, 
						supplyNm        : {type : "string", editable: true},
						preDate         : {type : "date"  , format: "{0:yyyy-MM-dd}", editable: false},
						preNo           : {type : "string", editable: false},
						poDate          : {type : "date"  , format: "{0:yyyy-MM-dd}", editable: false},
						poNo            : {type : "string", editable: false},
						centerCd        : {type : "string", editable: false},
						shipperCd       : {type : "string", editable: true},
						orgInboundDate  : {type : "date"  , format: "{0:yyyy-MM-dd}", editable: false},
						orgInboundNo    : {type : "string", editable: false},
						orgOrderNo    	: {type : "string", editable: false},
						// inboundDcnt		: {type : "string", editable: false}
						inboundDcnt         : {type : "number", format: "{0:#,##0}", editable: false}
					}
				}
			}
		});

		var grid = $("#gridMain").kendoGrid({
			dataSource : g_mainDataSource,
			pageable: false,
			selectable: "row",
			sortable : true,
			resizable: true,
			dataBound: fn_masterDataBound,
			navigatable: true,
			allowCopy: true,
			filterable:  {
				extra : false,
				messages : {
					info : "필터 옵션:",
					isTrue : "참인지",
					isFalse : "거짓인지",
					filter : "적용",
					clear : "초기화",
					and : "AND",
					or : "OR",
					selectValue : "-값을 선택하세요-",
					operator : "연산자",
					value : "값",
					cancel : "취소"
				},
				operators : {
					string : {
						contains : "~를 포함하는",
						eq : "~와 같은",
						neq : "~와 같지 않은",
						startswith : "~를 시작으로 하는",
						endswith : "~을 마지막으로 하는"
					},
					number : {
						eq : "~와 같은",
						neq : "~와 같지 않은",
						gte : "~보다 크거나 같은",
						gt : "~보다 큰",
						lte : "~보다 작거나 같은",
						lt : "~보다 작은"
					},
					date : {
						eq : "~와 같은",
						neq : "~와 같지 않은",
						gte : "~보다 크거나 같은",
						gt : "~보다 큰",
						lte : "~보다 작거나 같은",
						lt : "~보다 작은"
					},
					enums: {
				        eq: "~와 같은",
				        neq: "~와 같지 않은"
				    }
				}
			},				
			columns : [ 
		         {template : "<input type='checkbox' class='ChkBoxMainGrid' name='chkBox' id='checkBoxId'/>" 
		        	 ,title : "<input id='checkMasterAll' type='checkbox' class='check-box' onclick='javascript:fn_goMasterCheckAll();'/>", width: "30px"}, 
		         {field : "inboundDate",     title :"입고일자", type : "date"  , format: "{0:yyyy-MM-dd}",  width    : "100px"}, 
		         {field : "inboundNo", 		 title :"입고번호",   width    : "80px"}, 
		         {field : "inboundDivNm",  	 title :"입고구분",   editor: inboundDivDropDownEditor, width    : "100px"},
		         {field : "supplyCd",        title :"공급처코드", width    : "120px" ,    messages: {search: "Search category"}, attributes:{style:"text-align:center;"}, template : "<span>#= supplyCd #</span>&nbsp;&nbsp;<a href='javascript:fn_supplyPopup()' id='popupIcon' class='k-icon k-i-search'></a>"}, 
		         {field : "supplyNm",   	 title :"공급처명"  ,   width    : "180px", attributes:{style:"text-align:left;"}}, 
		         {field : "preDate",     	 title :"예정일자", type : "date"  , format: "{0:yyyy-MM-dd}",  width    : "100px"},
		         {field : "preNo",      	 title :"예정번호",   width    : "80px"}, 
		         {field : "poDate",        	 title :"발주일자", type : "date"  , format: "{0:yyyy-MM-dd}",  width    : "100px"},

		         { field: "poNo", title: "발주번호", type: "string", width: "80px"  }, 
		         { field: "orgOrderNo", title: "원주문코드", type: "string", width: "100px"  }, 
		                 
		         //{field : "poNo",  			 title :"발주번호",   width    : "80px" },
		         {field : "",  			 title :""}
			],
			editable : "incell",
			change : function (e) {
				
				/*서한수 처리!!!!!!!!*/
				
				var v_grid = $("#gridMain").data("kendoGrid");
				var v_dataItem = v_grid.dataItem(v_grid.select());
				
				var v_currentRow = v_grid.tbody.find("tr[data-uid='" + v_dataItem.uid + "']");
				g_currentRow = v_currentRow;
				
			      var input = $("<input/>");
       	 	      input.attr("name", "poNo");
       	 	      input.attr("placeholder", "11111111111111111111111111111");
       	 	      input.appendTo(e.container);
				
				
				fn_gridControl("change", null);
			},
			edit : function(e) {
				
			      var input = $("<input/>");
       	 	      //input.attr("name", "poNo");
       	 	      input.attr("placeholder", "11111111111111111111111111111");
       	 	      input.appendTo(e.container);
				
				
				/*
    	    	var v_columnIndex = this.cellIndex(e.container);
		        var v_fieldName   = this.thead.find("th").eq(v_columnIndex).data("field");	
		        
		        */
				/*그리드 선택값 name 값 확인 작업  20160117 */
				$('#multiHeaderMasterArea').html(e.container.find("td.k-edit-cell").prevObject.context.innerHTML);
    	    	
    	    	var v_fieldName = '';
    	    	if(1 < $('#multiHeaderMasterArea input').length){
    	    	  $("#multiHeaderMasterArea input").each(function(index) {
                    if(undefined != $(this).attr('name')){
                    	v_fieldName = $(this).attr('name')
                    	}
            		});
    	    	}else{
    	    		v_fieldName = $('#multiHeaderMasterArea input').attr('name');	
    	    	}
    	    	
		        if (!fn_isEditable(v_fieldName, e.model)) {
	    	    	this.closeCell();
	    	    	return;
	    	    }
		        /* 20161230 장원봉 디테일 그리드에 데이터 존재여부 체크 수정 */
				var v_grid     = $("#gridMain").data("kendoGrid");
				var v_dataItem = v_grid.dataItem(v_grid.select());	
				
				if (v_dataItem !=null && v_dataItem.crud != "C") {		//수정
		        	fn_slaveDataCheck(v_grid, v_dataItem.centerCd, v_dataItem.shipperCd, v_dataItem.inboundNo, v_dataItem.inboundDate, v_fieldName);
				
				} else {												//신규
			        if (!fn_isEditable(v_fieldName, e.model)) {
		    	    	this.closeCell();
		    	    	return;
		    	    }
				}
			}
		}).data("kendoGrid");
		
		grid.table.on("click", ".ChkBoxMainGrid" , fn_selectRow);
		
		grid.table.on("click", "#popupIcon" , fn_selectRow);
		
		
	} //그리드(Master) 처리 Function End
	/* 
 	function fn_check() {
		
        if($("#checkBoxId").is(":checked")){
            alert("체크박스 체크했음!");
            
        }else{
            alert("체크박스 체크 해제!");
        }
	}  */
	
	/* 20161230 장원봉 디테일 그리드에 데이터 존재여부 체크 수정 */
	/*======================================================*
	 * - Function 명   : fn_slaveDataCheck                  *
	 * - Function 기능 : Slave 그리드 데이터 카운트 조회    *
	 * - param : centerCd 물류센터 코드                     *
	 * - param : csrCd 화주코드                             *
	 * - param : inboundNo 입고번호                         *
	 * - param : inboundDate 입고날짜                       *
	 *======================================================*/
	function fn_slaveDataCheck(v_grid, centerCd, shipperCd, inboundNo, inboundDate, fieldName) {
		
		 var v_params = {};
		v_params['centerCd'] = centerCd;
		v_params['shipperCd'] = shipperCd;
		v_params['inboundNo'] = inboundNo;
		
		if (inboundDate != null) {
			v_params['inboundDate'] = inboundDate.yyyymmdd();	
		}
		
		$.ajax({
			type :"post",
			url  : "/wms/inbound/selectInboundReadyDtlCnt.do",
			datatype : "json",
			data     : v_params,
	        beforeSend : function(request){
	        	request.setRequestHeader("guserid", GLB_USER.userid); 
	        	request.setRequestHeader("gdbinfo", GLB_USER.dbinfo);
	        	request.setRequestHeader("guserkey", GLB_USER.userkey);
	        },				
			success : function(result) {
				if (result.result > 0) {	// 디테일 데이터가 있다면..
					v_grid.closeCell();	
				}	
			}
		});
	}

	/*======================================================*
	 * - Function 명   : fn_slaveGrid                       *
	 * - Function 기능 : 디테일 그리드처리                  *   
	 *======================================================*/
	function fn_slaveGrid() {
			g_slaveDataSource = new kendo.data.DataSource({
				batch : true,
				//pageSize : 50,
		 	   	transport: {
					read : function(options) {
						$.ajax({
							url : "/wms/inbound/selectInboundReadyDtl.do",
							dataType : "jsonp",
					        beforeSend : function(request){
					        	request.setRequestHeader("guserid", GLB_USER.userid); 
					        	request.setRequestHeader("gdbinfo", GLB_USER.dbinfo);
					        	request.setRequestHeader("guserkey", GLB_USER.userkey);
					        },
					        data : g_srchSlaveParams,
							success : function(result) {
								options.success(result);
								fn_checkboxAll("subChkBox", false);
								$("#checkSlaveAll").prop("checked", false);
								g_checkedSlaveIds  = {};	// 슬레이브 그리드 체크박스 전역변수 초기화
								g_srchSlaveParams = {};
								g_srchMsgPrint = "N";
							},
							error : function(x, o, e) {
								showExceptionError(x, o, e);
							}	
						});
					}
				},
				requestEnd: function(e) {
					console.log('### requestEnd: ');
					

					debugger;
					
				},
				edit : function(e) {
					debugger;
				},
				change: function(e) {
					
					console.log('### slave change in : ');
					
					var v_sGrid = $("#gridSlave").data("kendoGrid");
					/* ====== Slave 그리드 셀 수정이 발생했을 때 ===== */

			        var v_grid = $("#gridMain").data("kendoGrid");
			    	var selectedRowIndex =	 $('#gridSlave').data("kendoGrid").select().index();
					
			        
					var v_dataItem = v_grid.dataItem(v_grid.select());
					if (v_dataItem != null) {
						g_masterRow = null;
						g_masterUid = v_dataItem.uid; 												// 2. 마스터 그리드의 현재 선택 UID 전역변수 저장
						g_masterRow = v_grid.tbody.find("tr[data-uid='" + v_dataItem.uid + "']");	// 3. 마스터 그리드이 현재 선택 TR(row)를 전역변수 저장
					}
					
					if (e.field != null && e.action == "itemchange") {
						// 
						// 팝업을 통해서 바인딩 되는 경우 현재 Row를 가져오는 부분 수정 / 팝업 OR 팝업이 아닌경우로 나뉨 20170327.김범수
						var v_sGrid = $("#gridSlave").data("kendoGrid");	// 1. CHECKBOX 체크 처리
						var v_sDataItem = v_sGrid.dataItem(v_sGrid.select());
						
				        var v_sCurrentRow = null;
						if( (null == v_sDataItem) || (undefined == v_sDataItem) ){
							v_sCurrentRow = g_popupCurrentRow;
						}else{
							v_sCurrentRow = v_sGrid.tbody.find("tr[data-uid='" + v_sDataItem.uid + "']");
							g_checkedSlaveIds[v_sDataItem.id] = true;
						}
						v_sCurrentRow.find(".ChkBoxMainGrid").prop("checked","checked");
						v_sCurrentRow.addClass("k-state-selected");
						// end 20170327.김범수
						
						
						console.log("인덱스!!!!!!!!" + $('#gridSlave').data("kendoGrid").cellIndex($('#gridSlave').data("kendoGrid").select().index()));
					
						
						debugger;
						
						console.log("e.field!!!!!!" + e.field);
						if (e.field == "storageDivNm" ) {
						//$("#gridSlave").data('kendoGrid').editCell($("#gridSlave").find('.k-grid-content tr:eq(0) td:eq(10)'));
						$("#gridSlave").find('.k-grid-content tr:eq('+ selectedRowIndex +') td:eq(5)').click();
						} else if (e.field ==  "prodStatusNm") {
							//$("#gridSlave").data('kendoGrid').editCell($("#gridSlave").find('.k-grid-content tr:eq(0) td:eq(10)'));
							$("#gridSlave").find('.k-grid-content tr:eq('+ selectedRowIndex +') td:eq(6)').click();
						} else if (e.field ==  "lot") {
							//$("#gridSlave").data('kendoGrid').editCell($("#gridSlave").find('.k-grid-content tr:eq(0) td:eq(13)'));
							  setTimeout(function () {    //refocusing the cell
							        return function () {
										$("#gridSlave").find('.k-grid-content tr:eq('+ selectedRowIndex +') td:eq(13)').click();
							        }
							    }(), 100);
						}
						
							
						
						
					    /*
						현재 사용하지 않아서 주석처리 20170327.김범수
						20161230 장원봉 상품코드 직접 입력 시처리..
						if (e.field == "prodCd") {
							var param = {};
			            	param['callFlag']  = 'keyup';				// 쿼리 분기를 위해..
			            	param['shipperCd'] = GLB_USER.shipperCd;	// 화주
			            	param['supplyCd']  = v_dataItem.supplyCd;	// 공급처 코드
			            	param['prodCd']    = v_sDataItem.prodCd;	// 상품 코드
			            	fn_setChildValue()
			            	fn_getProductCd(param); 
						}
						*/
						
						if (e.field == "excQty" ) {
							
							if (e.items[0].excQty == null || e.items[0].excQty < 1 ) {
							   v_sDataItem.set("excPltQty", 0);	
							} else if ( e.items[0].prodPltQty == null ||  e.items[0].prodPltQty == 0  ) {
							   v_sDataItem.set("excPltQty", 0);
							} else if ( e.items[0].prodPkgQty == null ||  e.items[0].prodPkgQty == 0  ) {
								   v_sDataItem.set("excPltQty", 0);							   
							} else if ( e.items[0].excQty >= (e.items[0].prodPltQty * e.items[0].prodPkgQty)  ) {
								v_sDataItem.set("excPltQty", Math.floor(e.items[0].excQty / (e.items[0].prodPltQty * e.items[0].prodPkgQty)));
							} else  {
								v_sDataItem.set("excPltQty", 0);
							}
							
							if (e.items[0].excQty == null || e.items[0].excQty < 1 ) {
							   v_sDataItem.set("excPkgQty", 0);		
							} else if (  e.items[0].prodPkgQty == null ||  e.items[0].prodPkgQty == 0  ){
							   v_sDataItem.set("excPkgQty", 0);
							} else if ( e.items[0].prodPltQty == null ||  e.items[0].prodPltQty == 0  ) {
								v_sDataItem.set("excPkgQty", Math.floor(e.items[0].excQty / e.items[0].prodPkgQty));	
							} else if ( e.items[0].excQty % (e.items[0].prodPltQty * e.items[0].prodPkgQty) >= e.items[0].prodPkgQty ) {
								v_sDataItem.set("excPkgQty",  Math.floor(e.items[0].excQty % (e.items[0].prodPltQty * e.items[0].prodPkgQty) / e.items[0].prodPkgQty));
							} else  {
								v_sDataItem.set("excPkgQty", 0);
							}

					        if (e.items[0].excQty == null || e.items[0].excQty < 1 ) {
							   v_sDataItem.set("excEaQty", 0);		
							} else if  ((e.items[0].prodPkgQty == null ||  e.items[0].prodPkgQty == 0) || (e.items[0].prodPkgQty > e.items[0].excQty)  ) {
							   v_sDataItem.set("excEaQty", e.items[0].excQty);
							} else if ((e.items[0].prodPltQty == null ||  e.items[0].prodPltQty == 0  ) && (e.items[0].prodPkgQty < e.items[0].excQty)) {
								v_sDataItem.set("excEaQty", Math.floor(e.items[0].excQty % e.items[0].prodPkgQty) );
							}  else  {
						   		v_sDataItem.set("excEaQty", Math.floor((e.items[0].excQty % (e.items[0].prodPltQty * e.items[0].prodPkgQty))  %  e.items[0].prodPkgQty));
							}
						
						}
						
						debugger;
						
					}
				},
				schema : {
					data : "data",
					model : {
						id : "gridSeq",
						fields : {
							subChkBox      : {type : "boolean"},
							inboundStatus   : {type : "string", editable: false},
							inboundStatusNm : {type : "string", editable: false},
							inboundSeq     : {type : "string", editable: false},
							prodCd         : {type : "string", editable: true},
							inboundDate     : {type : "date"  , format: "{0:yyyy-MM-dd}", editable: true},
							prodNm         : {type : "string", editable: true},
							prodStatus     : {type : "string", editable: false},
							prodStatusNm   : {editable: true},
							lot            : {type : "string", editable: true},
							prodPltQty     : {type : "number", format: "{0:#,##0}", editable: true, validation: { required: true, min: 0}},
							prodPkgQty     : {type : "number", format: "{0:#,##0}", editable: true, validation: { required: true, min: 0}},
							
							preQty         : {type : "number", format: "{0:#,##0}", editable: false},
							prePltQty      : {type : "number", format: "{0:#,##0}", editable: false},
							prePkgQty      : {type : "number", format: "{0:#,##0}", editable: false},
							preEaQty       : {type : "number", format: "{0:#,##0}", editable: false},

							
							excQty         : {type : "number", format: "{0:#,##0}", editable: true, validation: { required: true, min: 0}},
							excPltQty      : {type : "number", format: "{0:#,##0}", editable: true, validation: { required: true, min: 0}},
							excPkgQty      : {type : "number", format: "{0:#,##0}", editable: true, validation: { required: true, min: 0}},
							excEaQty       : {type : "number", format: "{0:#,##0}", editable: true, validation: { required: true, min: 0}},
							
							prodSizeWidth  : {type : "string", editable: true},
							prodSizeDepth  : {type : "string", editable: true},
							prodSizeHeight : {type : "string", editable: true},
							prodWeigth     : {type : "string", editable: true},
							prodVol        : {type : "string", editable: true},
							
							shelfLifeDIv   : {type : "string", editable: false},
							shelfLifeDivNm : {type : "string", editable: true},
							storageDiv     : {type : "string", editable: false},
							storageDivNm   : {type : "string", editable: true},
							
							inboundReadyDatetime   : {type : "date", format: "{0:yyyy-MM-dd h:mm:ss tt}", editable: false},
							inboundReadyId         : {type : "string", editable: false},
							inboundModifyDatetime  : {type : "date", format: "{0:yyyy-MM-dd h:mm:ss tt}", editable: false},
							inboundModifyId        : {type : "string", editable: false},
							
							preSeq         : {type : "string", editable: false},
							poSeq          : {type : "string", editable: false},
							gridSeq        : {type : "string", editable: false},
							aa        : {type : "string", editable: false}
						}
					}
				}
			});
			
		var grid = $("#gridSlave").kendoGrid({
			dataSource : g_slaveDataSource,
		    pageable: false,
				selectable: "row",
				sortable : true,
				resizable: true,
				dataBound: fn_slaveDataBound,
				navigatable: true,
				filterable:  {
					extra : false,
					messages : {
						info : "필터 옵션:",
						isTrue : "참인지",
						isFalse : "거짓인지",
						filter : "적용",
						clear : "초기화",
						and : "AND",
						or : "OR",
						selectValue : "-값을 선택하세요-",
						operator : "연산자",
						value : "값",
						cancel : "취소"
					},
					operators : {
						string : {
							contains : "~를 포함하는",
							eq : "~와 같은",
							neq : "~와 같지 않은",
							startswith : "~를 시작으로 하는",
							endswith : "~을 마지막으로 하는"
						},
						number : {
							eq : "~와 같은",
							neq : "~와 같지 않은",
							gte : "~보다 크거나 같은",
							gt : "~보다 큰",
							lte : "~보다 작거나 같은",
							lt : "~보다 작은"
						},
						date : {
							eq : "~와 같은",
							neq : "~와 같지 않은",
							gte : "~보다 크거나 같은",
							gt : "~보다 큰",
							lte : "~보다 작거나 같은",
							lt : "~보다 작은"
						},
						enums: {
					        eq: "~와 같은",
					        neq: "~와 같지 않은"
					    }
					}
				},				
				columns : [ 
		         {template : "<input type='checkbox' class='ChkBoxMainGrid' name='subChkBox' />"
		        	 ,title : "<input id='checkSlaveAll' type='checkbox' class='check-box' onclick='javascript:fn_goSlaveCheckAll();'/>", width: "30px"},
		       {field : "inboundStatusNm", title :"진행단계",  template:'#=template_Clr_Align(inboundStatusNm, "left", g_fontStyles )#', width    : "100px", attributes:{style:"text-align:center;"}},
		         {field : "inboundSeq",   title :"순번" , 	width : "60px", attributes:{style:"text-align:center;"}}, 
		         {field : "prodCd",       title :"상품코드",      width : "120px",attributes:{style:"text-align:center;"}, template : "<span>#= prodCd #</span>&nbsp;&nbsp;<a href='javascript:fn_prodPopup()' id='popupIconD' class='k-icon k-i-search'></a>"}, 
		         {field : "prodNm",       title :"상품명",        width : "180px", attributes:{style:"text-align:left;"}}, 
		         {field : "prodStatusNm", title :"상품상태",editor: prodStsDropDownEditor, width : "100px"},
		         {field : "lot",          title :"LOT",           width : "100px"},

		         {field : "shelfLifeDivNm", title :"유통기한구분", width    : "100px"},
		         {field : "storageDivNm",  	title :"보관구분", width    : "100px"},
		         
	             {
			      title : "<span class='center disb w100' style='height:23px; margin-top:8px; margin-bottom:-3px; text-align: center;'>예정수량</span>",
				  columns: [		         
		            {field : "preQty",    title :"수량",      format: "{0:#,##0}", width : "60px", attributes:{style:"text-align:right;"}},
		            {field : "prePltQty", title :"PLT", format: "{0:#,##0}",width : "50px", attributes:{style:"text-align:right;"}},
		            {field : "prePkgQty", title :"PKG", format: "{0:#,##0}",width : "50px", attributes:{style:"text-align:right;"}},
		            {field : "preEaQty",  title :"낱개", format: "{0:#,##0}",width : "50px", attributes:{style:"text-align:right;"}},
			       ]
                 },		
	             {
				  title : "<span class='center disb w100' style='height:23px; margin-top:8px; margin-bottom:-3px; text-align: center;'>입고수량</span>",
				  columns: [	
				   {field : "excQty",        title :"수량",      format: "{0:#,##0}", width : "60px", attributes:{style:"text-align:right;"}},
			       {field : "excPltQty", title :"PLT", format: "{0:#,##0}", width : "50px", attributes:{style:"text-align:right;"}},
			       {field : "excPkgQty", title :"PKG", format: "{0:#,##0}", width : "50px", attributes:{style:"text-align:right;"}},
			       {field : "excEaQty",  title :"낱개", format: "{0:#,##0}", width : "50px", attributes:{style:"text-align:right;"}},
			       ]
                 },	
			     {field : "prodWeigth",     title :"중량",  		width    : "60px", attributes:{style:"text-align:right;"}},
		         {field : "prodVol",        title :"체적",  		width    : "60px", attributes:{style:"text-align:right;"}},
		         {field : "prodSizeWidth",  title :"가로",  			    width    : "60px", attributes:{style:"text-align:right;"}},
		         {field : "prodSizeDepth",  title :"세로",  				width    : "60px", attributes:{style:"text-align:right;"}},
		         {field : "prodSizeHeight", title :"높이",  				width    : "60px", attributes:{style:"text-align:right;"}},

		         {field : "prodPltQty",   title :"PLT<br>입수", format: "{0:#,##0}",   width : "50px", attributes:{style:"text-align:right;"}},
		         {field : "prodPkgQty",   title :"PKG<br>입수", format: "{0:#,##0}",   width : "50px", attributes:{style:"text-align:right;"}},
		         {field : "inboundReadyDatetime", title :"대기일시", type:"date", 	format: "{0:yyyy-MM-dd h:mm:ss tt}", width    : "160px"},
		         {field : "inboundReadyId", title :"대기처리자",    	width    : "90px", attributes:{style:"text-align:left;"}},
		         {field : "inboundModifyDatetime", 	title :"변환일시", type:"date", format: "{0:yyyy-MM-dd h:mm:ss tt}", width    : "160px"},
		         {field : "inboundModifyId", 	title :"변환작업자",width    : "80px", attributes:{style:"text-align:left;"}},
		         {field : "preSeq", 			title :"예정순번",  	width    : "70px", attributes:{style:"text-align:center;"}},
		         {field : "poSeq",		 		title :"발주순번",  	width    : "70px", attributes:{style:"text-align:center;"}},
		         {field : "",  			 title :""}
	         
				],
				editable : true,
				change : function(e) {
					
					
					// 
					console.log('### slave change`');
					// 팝업을 통해서 바인딩 되는 경우 현재 Row를 가져오는 부분 수정 / 팝업 OR 팝업이 아닌경우로 나뉨 20170327.김범수
					var v_sGrid = $("#gridSlave").data("kendoGrid");
					var v_sDataItem = v_sGrid.dataItem(v_sGrid.select());
					
					if( (null == v_sDataItem) || (undefined == v_sDataItem) ){
						g_slaveCurrentRow = g_popupCurrentRow;
					}else{
						var v_sCurrentRow = v_sGrid.tbody.find("tr[data-uid='" + v_sDataItem.uid + "']");
						g_slaveCurrentRow = v_sCurrentRow;
					}
					// end 20170327.김범수
					
					fn_secGridControl("change", null);
				},
				edit : function(e) {

					console.log('### edit!!!!!!!!!!!!!!!!!!!!: ');
	    	    	
					debugger;
					
					/*var v_sColumnIndex = this.cellIndex(e.container);
			        var v_sFieldName   = this.thead.find("th").eq(v_sColumnIndex).data("field");*/
			        
					/*그리드 선택값 name 값 확인 작업  20160117 */
					$('#multiHeaderDetailArea').html(e.container.find("td.k-edit-cell").prevObject.context.innerHTML);
	    	    	
	    	    	var v_sFieldName = '';
	    	    	if ( 1  <  $('#multiHeaderDetailArea input').length) {
	    	    	  $("#multiHeaderDetailArea input").each(function(index) {
	                    if(undefined != $(this).attr('name')){
	                      v_sFieldName = $(this).attr('name')
	                    	}
	            		});
	    	    	  
	    	    	} else {
	    	    		v_sFieldName = $('#multiHeaderDetailArea input').attr('name');	
	    	    	}
			        if (v_sFieldName == "excQty") {
			        	if (!fn_isEditable(v_sFieldName, e.model)) {
							fn_confirmPopupOpen("상품코드를 입력 후 수량을 입력해 주십시오.", "입고대기");
							return false;
			        	}
					}
			        
			        if (!fn_isEditable(v_sFieldName, e.model)) {
		    	    	this.closeCell();
		    	    	return;
		    	    }
				}
		}).data("kendoGrid");
		
        $(document.body).keydown(function(e) {

			console.log('### 엔터침: ');

			console.log('### 엔터침: ');
			
			
        	
            if (e.keyCode == 13) {
            	

				console.log("!!!!!!!!!!!!!!!!!!!!!!!!!!!!!엔터침!  !!!!!!!!!!!!!!!!!!!!!!!");
            	
            //    $("#grid").data("kendoGrid").table.focus();
            }
        });
		
		grid.table.on("click", ".ChkBoxMainGrid" , fn_selectSlaveRow);
		
		grid.table.on("click", "#popupIconD" , fn_selectSlaveRow);
			
			function createMultiSelect(element) {
				element.removeAttr("data-bind");

				element.kendoMultiSelect({
					dataSource: names,
					change: function(e) {
						var filter = { logic: "or", filters: [] };
						var values = this.value();
						$.each(values, function(i, v) {
							filter.filters.push({field: "name", operator: "eq", value: v });
						});
						dataSource.filter(filter);
					}
				});
			}			
	}  //그리드(slave) 처리 Function End
</script>		
         
<!-- 메시지 다이얼로그 DIV-->
<div class="dialog_wrap" id="confirmDiv">
   	<p class="message" style="text-align:center">#= cont #</p>
</div>

<script id="confirm-dlg" type="text/x-kendo-template">
<div class="message-p">
    <p class="message">#= cont #</p>
    <br>
    <button class="confirm-ok k-button">확인</button>
</div>
</script>

<!-- content-area:start -->
<div id="body">
	<div id="content-area" class="gr_top_bx">
		<div class="cont_def" id="boxSc">

			<!-- ########################## TOOLBAR 영역 ##########################-->
			<div class="tool-top-area">
				<div class='f_left'>
					<%@ include file="/include/btn_comm.jsp" %>
				</div>
				<div  class='f_right' >
					<input type="button"  class="button btn_point" value="입고실행" id="btnInboundExc" onclick="javascript:fn_inboundDateChangeCheck();"/>
				</div>
			</div>
			<!-- ########################## TOOLBAR 영역:end ########################## -->
			<!-- Grid & Dialog 영역:-->
			<!-- ########################## 그리드영역 ################################ -->
			<div class="box_02_fl grid_area">
				<div class="grid_content">
					<div id="splitter_ud" style="height: 100%">
						<div id="top-pane">
							<div class="t_inner">
								<div class="title_area">
									<h3><fmt:message key='ibd.ibdPre.title'/></h3>
								</div>
								<div id="gridMain" class="grid_tem"></div>
							</div>
						</div>
						<div id="bottom-pane">
							<div class="b_inner">
								<div class="title_area">
									<h3><fmt:message key='ibd.ibdPre.dtitle'/></h3>
								</div>
								<div id="gridSlave" class="grid_tem"></div>
							</div>
						</div>
					</div>
				</div>
			</div>
			<!-- ########################## 그리드영역 ################################ -->


		</div>

		<!-- content -->
	</div>
</div>


<!-- content-area:end -->
<!-- ########################## 검색버튼 우클릭 영역 ################################ -->
<div class="right_touch"></div>
<div class="right_slide" style="padding-left: -88px; margin-left: -88px;">
	<div class="inner">
		<div class="search_box">
			<div class="title_box">
				<span class="t_p">검색</span>
			</div>
			<div class="rs_box1">
				<div class="table2">
					<table class="input_tbl">
						<tbody>		
							<tr>
							<th class="left"><fmt:message key='ibd.chkPre.ibddtm'/></th><!-- 입고일자 -->
								<td>
									<input value="" class="strdtm shtwo_input" id="ibdStrDt"/>&nbsp;~&nbsp;
									<input value="" class="enddtm shtwo_input" id="ibdEndDt"/>
									<input type="hidden" id="sc_taskd3" />
								</td>
							</tr>
							
							<tr>
								<th class="left"><fmt:message key='ibd.guide.no'/></th><!-- 입고번호 -->
								<td><input type="text" id="ibdNo" class="tbx_style w100"></td>
							</tr>
							<tr>
								<th class="left"><fmt:message key='ibd.guide.ibddiv'/></th><!-- 입고구분 -->
								<td>
									<select class="popup_select w100" id="ibdDivCd">
										<option value="all"><fmt:message key='ibd.select.all'/></option>
										<c:if test="${not empty inboundDivCode}">
											<c:forEach items="${inboundDivCode}" var="data" varStatus="status">
												<option value="${data.sCommonCd}">${data.sCommonNm}</option>
											</c:forEach>
										</c:if>
									</select>
								</td>
							</tr>
							<%--	사용안함 20170105.김범수 
							<tr>
								<th class="left"><fmt:message key='ibd.search.ord_snm'/></th><!-- 진행단계 -->
								<td><select class="popup_select w100" id="statusCd">
									<option value="all"><fmt:message key='ibd.select.all'/></option>
									<c:if test="${not empty inboundStsCode}">
										<c:forEach items="${inboundStsCode}" var="data" varStatus="status">
											<option value="${data.sCommonCd}">${data.sCommonNm}</option>
										</c:forEach>
									</c:if>
								</select></td>
							</tr>
							 --%>
						</tbody>
					</table>                 
					<div class="btn_area_02 tac mgt_5">
						<a href="javascript:fn_srchPnlInit();" class="button btn_default" id="btn_srchpnl_init"><span class="btn_text"><fmt:message key='ibd.button.srch_init'/></span></a> 
						<a href="javascript:fn_btnSearchPnlClick();" class="button btn_point"   id="btn_srchpnl_srch"><span class="btn_text"><fmt:message key='ibd.button.srch'/></span></a>
					</div>
				</div>
			</div>
		</div>

		<!-- 오픈메뉴영역 -->
		<div id="openMenu"></div>
		<!-- 오픈메뉴영역 -->

	</div>
</div>

<!-- ########################## 검색버튼 우클릭 영역 ################################ -->
<!-- content-area:end -->
<div id="gridSupplyPopup" class="grid_tem"></div>
<div id="gridProductPopup" class="grid_tem"></div>
<div id="gridDateChangePopup" class="grid_tem"></div>
<div id="loading" style="display: none;"></div>
<div id="multiHeaderMasterArea" style="visibility: hidden" ></div>
<div id="multiHeaderDetailArea" style="visibility: hidden" ></div>
<div id="ckeckBoxTemp1" style="display: none;"></div>
<div id="ckeckBoxTemp2" style="display: none;"></div>

<!-- Kendo Grid   -->
<div id="windowcontainer" ></div>

<!-- Kendo Multi Header 
 Header 컨트롤을 위한 그리드추가 2016.01.17
 -->


