<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>공무원 초과근무수당 계산기</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- FontAwesome for Icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <!-- Noto Sans KR Font -->
    <link href="https://fonts.googleapis.com/css2?family=Noto+Sans+KR:wght@300;400;500;600;700&display=swap" rel="stylesheet">
    <script>
        tailwind.config = {
            theme: {
                extend: {
                    fontFamily: {
                        sans: ['Noto Sans KR', 'sans-serif'],
                    },
                    colors: {
                        govNavy: '#1a365d',
                        govBlue: '#2b6cb0',
                        govLightBlue: '#ebf8ff',
                        govAccent: '#3182ce',
                    }
                }
            }
        }
    </script>
    <style>
        body {
            font-family: 'Noto Sans KR', sans-serif;
            background-color: #f7fafc;
            color: #2d3748;
        }
        /* Custom Scrollbar for tables */
        .custom-scrollbar::-webkit-scrollbar {
            height: 6px;
            width: 6px;
        }
        .custom-scrollbar::-webkit-scrollbar-track {
            background: #f1f1f1;
            border-radius: 4px;
        }
        .custom-scrollbar::-webkit-scrollbar-thumb {
            background: #cbd5e0;
            border-radius: 4px;
        }
        .custom-scrollbar::-webkit-scrollbar-thumb:hover {
            background: #a0aec0;
        }
        @media print {
            .no-print {
                display: none !important;
            }
            .print-only {
                display: block !important;
            }
            body {
                background-color: white;
            }
        }
    </style>
</head>
<body class="min-h-screen flex flex-col bg-slate-50">

    <!-- Header Navigation -->
    <header class="bg-govNavy text-white shadow-md sticky top-0 z-50">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 h-16 flex items-center justify-between">
            <div class="flex items-center space-x-3">
                <div class="bg-white/10 p-2 rounded-lg">
                    <i class="fa-solid fa-calculator text-2xl text-blue-300"></i>
                </div>
                <div>
                    <h1 class="font-bold text-lg sm:text-xl tracking-tight leading-none">공무원 초과근무수당 계산기</h1>
                    <p class="text-xs text-blue-200 mt-1">공무원 수당 등에 관한 규정 제15조~제17조 기준</p>
                </div>
            </div>
            <div class="flex items-center space-x-2 no-print">
                <button id="resetAllBtn" class="text-xs bg-white/10 hover:bg-white/20 text-white px-3 py-1.5 rounded-md transition flex items-center gap-1.5">
                    <i class="fa-solid fa-rotate-left"></i>
                    <span class="hidden sm:inline">초기화</span>
                </button>
                <button id="printBtn" class="text-xs bg-govBlue hover:bg-blue-600 text-white px-3 py-1.5 rounded-md transition flex items-center gap-1.5">
                    <i class="fa-solid fa-print"></i>
                    <span class="hidden sm:inline">인쇄/PDF</span>
                </button>
            </div>
        </div>
    </header>

    <!-- Main Container -->
    <main class="flex-grow max-w-7xl w-full mx-auto px-4 sm:px-6 lg:px-8 py-6 space-y-6">

        <!-- Top Alert / Notice -->
        <div class="bg-blue-50 border-l-4 border-govBlue p-4 rounded-r-lg text-sm text-slate-700 shadow-sm flex items-start gap-3">
            <i class="fa-solid fa-circle-info text-govBlue text-lg mt-0.5"></i>
            <div class="space-y-1">
                <p class="font-semibold text-slate-800">2026년 봉급표 및 수당 규정 적용 안내</p>
                <p class="text-xs sm:text-sm text-slate-600">
                    본 계산기는 일반직, 경찰·소방, 교원 등 직종별 시간외근무수당, 야간수당, 휴일근무수당 단가를 자동 계산합니다. 
                    지방자치단체 및 중앙부처 지침에 따라 1시간 기본 공제 및 월 상한 시간(기본 57시간)이 설정됩니다.
                </p>
            </div>
        </div>

        <div class="grid grid-cols-1 lg:grid-cols-12 gap-6">

            <!-- Left Panel: Settings & Unit Price Calculation (4 Cols) -->
            <section class="lg:col-span-4 space-y-6">
                <!-- 1. Basic Settings Box -->
                <div class="bg-white rounded-xl shadow-sm border border-slate-200 p-5 space-y-4">
                    <div class="flex items-center justify-between border-b border-slate-100 pb-3">
                        <h2 class="font-bold text-slate-800 text-base flex items-center gap-2">
                            <i class="fa-solid fa-user-gear text-govBlue"></i>
                            1. 직급 및 단가 설정
                        </h2>
                        <span class="text-xs font-semibold px-2 py-0.5 rounded bg-blue-100 text-govBlue">수당 단가 산출</span>
                    </div>

                    <!-- Public Servant Type -->
                    <div>
                        <label for="jobType" class="block text-xs font-medium text-slate-600 mb-1">직종 구분</label>
                        <select id="jobType" class="w-full text-sm border border-slate-300 rounded-lg px-3 py-2 bg-slate-50 focus:bg-white focus:outline-none focus:ring-2 focus:ring-govBlue transition">
                            <option value="general">일반직 공무원</option>
                            <option value="police">경찰·소방 공무원</option>
                            <option value="teacher">교원 (초·중·고)</option>
                            <option value="custom">직접 입력 (단가 수동)</option>
                        </select>
                    </div>

                    <!-- Grade Selection -->
                    <div id="gradeContainer">
                        <label for="jobGrade" class="block text-xs font-medium text-slate-600 mb-1">직급 선택</label>
                        <select id="jobGrade" class="w-full text-sm border border-slate-300 rounded-lg px-3 py-2 bg-slate-50 focus:bg-white focus:outline-none focus:ring-2 focus:ring-govBlue transition">
                            <option value="g9">9급 (순경 / 소방교 이하)</option>
                            <option value="g8">8급 (경장 / 소방교)</option>
                            <option value="g7" selected>7급 (경사 / 소방장)</option>
                            <option value="g6">6급 (경위 / 소방위)</option>
                            <option value="g5">5급 (경감 / 소방경)</option>
                        </select>
                    </div>

                    <!-- Base Salary / Hourly Rate Toggle -->
                    <div class="pt-2 border-t border-slate-100 space-y-3">
                        <div>
                            <div class="flex justify-between items-center mb-1">
                                <label for="hourlyRateInput" class="text-xs font-semibold text-slate-700">시간외수당 단가 (시간당)</label>
                                <button id="toggleRateLock" class="text-xs text-govBlue hover:underline flex items-center gap-1">
                                    <i class="fa-solid fa-pen-to-square"></i> 직접 수정
                                </button>
                            </div>
                            <div class="relative">
                                <input type="number" id="hourlyRateInput" class="w-full text-sm font-bold text-slate-800 border border-slate-300 rounded-lg pl-3 pr-8 py-2 bg-slate-100 focus:outline-none focus:ring-2 focus:ring-govBlue" readonly value="12184">
                                <span class="absolute right-3 top-2.5 text-xs text-slate-500 font-medium">원</span>
                            </div>
                            <p class="text-[11px] text-slate-500 mt-1" id="rateFormulaNotice">
                                Formula: (봉급기준액 × 0.55 × 1/209) × 1.5
                            </p>
                        </div>

                        <!-- Secondary Allowances (Night & Holiday) Rate -->
                        <div class="grid grid-cols-2 gap-2 text-xs pt-1">
                            <div class="bg-slate-50 p-2.5 rounded-lg border border-slate-200">
                                <span class="text-slate-500 block mb-0.5">야간수당 단가</span>
                                <span id="nightRateText" class="font-bold text-slate-700">4,061 원</span>
                                <span class="text-[10px] text-slate-400 block mt-0.5">(봉급기준액×0.55×1/209×0.5)</span>
                            </div>
                            <div class="bg-slate-50 p-2.5 rounded-lg border border-slate-200">
                                <span class="text-slate-500 block mb-0.5">휴일수당 일액</span>
                                <span id="holidayRateText" class="font-bold text-slate-700">97,472 원</span>
                                <span class="text-[10px] text-slate-400 block mt-0.5">(봉급기준액×0.55×1/26×1.5)</span>
                            </div>
                        </div>
                    </div>

                    <!-- 2. Rule Options -->
                    <div class="pt-3 border-t border-slate-100 space-y-3">
                        <span class="block text-xs font-semibold text-slate-700">규정 및 공제 설정</span>
                        
                        <!-- 1시간 공제 옵션 -->
                        <label class="flex items-center justify-between cursor-pointer text-xs text-slate-700 p-2 bg-slate-50 rounded-lg hover:bg-slate-100 transition">
                            <span class="flex items-center gap-1.5">
                                <i class="fa-solid fa-clock-rotate-left text-amber-500"></i>
                                평일 1시간 자동 공제
                            </span>
                            <input type="checkbox" id="deductOneHourToggle" class="w-4 h-4 text-govBlue rounded focus:ring-govBlue" checked>
                        </label>

                        <!-- 월 최대 시간 설정 -->
                        <div class="flex items-center justify-between text-xs text-slate-700 p-2 bg-slate-50 rounded-lg">
                            <span class="flex items-center gap-1.5">
                                <i class="fa-solid fa-gauge-high text-red-500"></i>
                                월 최대 인정시간
                            </span>
                            <div class="flex items-center gap-1">
                                <input type="number" id="maxMonthlyHours" value="57" min="0" max="200" class="w-12 text-center text-xs font-semibold border border-slate-300 rounded py-1 focus:outline-none focus:ring-1 focus:ring-govBlue">
                                <span>시간</span>
                            </div>
                        </div>
                    </div>
                </div>

                <!-- Summary Cards (Mobile First Preview) -->
                <div class="bg-white rounded-xl shadow-sm border border-slate-200 p-5 space-y-4">
                    <h2 class="font-bold text-slate-800 text-base border-b border-slate-100 pb-3 flex items-center gap-2">
                        <i class="fa-solid fa-chart-pie text-govBlue"></i>
                        월간 합계 요약
                    </h2>

                    <div class="space-y-3">
                        <div class="flex justify-between items-center text-sm">
                            <span class="text-slate-600">인정 시간외 시간</span>
                            <span id="summaryTotalHours" class="font-bold text-slate-800">0 시간</span>
                        </div>
                        <div class="flex justify-between items-center text-sm">
                            <span class="text-slate-600">야간근무 시간</span>
                            <span id="summaryNightHours" class="font-bold text-slate-800">0 시간</span>
                        </div>
                        <div class="flex justify-between items-center text-sm">
                            <span class="text-slate-600">휴일근무 일수</span>
                            <span id="summaryHolidayDays" class="font-bold text-slate-800">0 일</span>
                        </div>
                        <div class="border-t border-dashed border-slate-200 pt-2 flex justify-between items-center">
                            <span class="text-sm font-semibold text-slate-700">총 지급 예상액</span>
                            <span id="summaryTotalPay" class="text-lg font-extrabold text-govBlue">0 원</span>
                        </div>
                    </div>
                </div>
            </section>

            <!-- Right Panel: Log Entry & Monthly Work Table (8 Cols) -->
            <section class="lg:col-span-8 space-y-6">

                <!-- Log Input Card -->
                <div class="bg-white rounded-xl shadow-sm border border-slate-200 p-5 space-y-4">
                    <div class="flex flex-wrap items-center justify-between border-b border-slate-100 pb-3 gap-2">
                        <h2 class="font-bold text-slate-800 text-base flex items-center gap-2">
                            <i class="fa-solid fa-calendar-plus text-govBlue"></i>
                            초과근무 등록 / 수정
                        </h2>
                        <span class="text-xs text-slate-500">근무 시작/종료 시각 입력 시 자동 계산</span>
                    </div>

                    <!-- Entry Form Grid -->
                    <div class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-4 gap-3">
                        <div>
                            <label for="workDate" class="block text-xs font-medium text-slate-600 mb-1">근무 일자</label>
                            <input type="date" id="workDate" class="w-full text-xs sm:text-sm border border-slate-300 rounded-lg px-2.5 py-2 focus:ring-2 focus:ring-govBlue focus:outline-none">
                        </div>
                        <div>
                            <label for="workType" class="block text-xs font-medium text-slate-600 mb-1">근무 구분</label>
                            <select id="workType" class="w-full text-xs sm:text-sm border border-slate-300 rounded-lg px-2.5 py-2 focus:ring-2 focus:ring-govBlue focus:outline-none">
                                <option value="weekday">평일 시간외</option>
                                <option value="holiday">휴일 근무</option>
                            </select>
                        </div>
                        <div>
                            <label for="startTime" class="block text-xs font-medium text-slate-600 mb-1">시작 시간</label>
                            <input type="time" id="startTime" value="18:00" class="w-full text-xs sm:text-sm border border-slate-300 rounded-lg px-2.5 py-2 focus:ring-2 focus:ring-govBlue focus:outline-none">
                        </div>
                        <div>
                            <label for="endTime" class="block text-xs font-medium text-slate-600 mb-1">종료 시간</label>
                            <input type="time" id="endTime" value="21:00" class="w-full text-xs sm:text-sm border border-slate-300 rounded-lg px-2.5 py-2 focus:ring-2 focus:ring-govBlue focus:outline-none">
                        </div>
                    </div>

                    <!-- Optional Description & Add Button -->
                    <div class="flex flex-col sm:flex-row gap-3 pt-1">
                        <input type="text" id="workMemo" placeholder="업무 내용 요약 (예: 국정감사 자료 작성, 긴급 현장 출동)" class="flex-grow text-xs sm:text-sm border border-slate-300 rounded-lg px-3 py-2 focus:ring-2 focus:ring-govBlue focus:outline-none">
                        <button id="addLogBtn" class="bg-govBlue hover:bg-blue-700 text-white font-medium text-sm px-5 py-2 rounded-lg transition flex items-center justify-center gap-2 shadow-sm">
                            <i class="fa-solid fa-plus"></i>
                            근무 추가
                        </button>
                    </div>
                </div>

                <!-- Monthly Work Log Table -->
                <div class="bg-white rounded-xl shadow-sm border border-slate-200 p-5 space-y-4">
                    <div class="flex items-center justify-between border-b border-slate-100 pb-3">
                        <div class="flex items-center gap-2">
                            <h2 class="font-bold text-slate-800 text-base">근무 내역목록</h2>
                            <span id="logCountBadge" class="bg-slate-100 text-slate-600 text-xs px-2 py-0.5 rounded-full font-semibold">0건</span>
                        </div>
                        <button id="clearAllLogsBtn" class="text-xs text-red-500 hover:text-red-700 flex items-center gap-1 no-print">
                            <i class="fa-solid fa-trash-can"></i> 전체 삭제
                        </button>
                    </div>

                    <!-- Table Container -->
                    <div class="overflow-x-auto custom-scrollbar">
                        <table class="w-full text-left border-collapse text-xs sm:text-sm">
                            <thead>
                                <tr class="bg-slate-100 text-slate-700 border-b border-slate-200">
                                    <th class="p-2.5 font-semibold">일자</th>
                                    <th class="p-2.5 font-semibold">구분</th>
                                    <th class="p-2.5 font-semibold">시간</th>
                                    <th class="p-2.5 font-semibold text-center">실제/인정</th>
                                    <th class="p-2.5 font-semibold text-center">야간</th>
                                    <th class="p-2.5 font-semibold">업무메모</th>
                                    <th class="p-2.5 font-semibold text-right">예상 수당</th>
                                    <th class="p-2.5 font-semibold text-center no-print">관리</th>
                                </tr>
                            </thead>
                            <tbody id="logTableBody" class="divide-y divide-slate-100">
                                <!-- Dynamic Rendered Rows -->
                                <tr id="emptyRow">
                                    <td colspan="8" class="text-center py-8 text-slate-400">
                                        <i class="fa-regular fa-calendar-xmark text-3xl mb-2 block"></i>
                                        등록된 초과근무 내역이 없습니다. 위에서 근무를 추가해주세요.
                                    </td>
                                </tr>
                            </tbody>
                        </table>
                    </div>
                </div>

                <!-- Detailed Pay Analysis & Deductions -->
                <div class="bg-white rounded-xl shadow-sm border border-slate-200 p-5 space-y-4">
                    <h2 class="font-bold text-slate-800 text-base border-b border-slate-100 pb-3 flex items-center gap-2">
                        <i class="fa-solid fa-file-invoice-dollar text-govBlue"></i>
                        상세 수당 및 공제(세금) 내역 추정
                    </h2>

                    <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                        <!-- Pay Category Breakdown -->
                        <div class="space-y-2.5">
                            <h3 class="text-xs font-semibold text-slate-500 uppercase tracking-wider">수당별 지급 내역</h3>
                            <div class="flex justify-between items-center p-2.5 bg-slate-50 rounded-lg">
                                <span class="text-xs text-slate-600">시간외근무수당</span>
                                <span id="breakdownOvertimePay" class="font-bold text-slate-800 text-sm">0 원</span>
                            </div>
                            <div class="flex justify-between items-center p-2.5 bg-slate-50 rounded-lg">
                                <span class="text-xs text-slate-600">야간근무수당</span>
                                <span id="breakdownNightPay" class="font-bold text-slate-800 text-sm">0 원</span>
                            </div>
                            <div class="flex justify-between items-center p-2.5 bg-slate-50 rounded-lg">
                                <span class="text-xs text-slate-600">휴일근무수당</span>
                                <span id="breakdownHolidayPay" class="font-bold text-slate-800 text-sm">0 원</span>
                            </div>
                            <div class="flex justify-between items-center p-2.5 bg-blue-50/70 rounded-lg border border-blue-100">
                                <span class="text-xs font-semibold text-govNavy">총 지급액 (세전)</span>
                                <span id="breakdownGrossPay" class="font-extrabold text-govBlue text-base">0 원</span>
                            </div>
                        </div>

                        <!-- Estimated Tax / Deductions -->
                        <div class="space-y-2.5">
                            <div class="flex items-center justify-between">
                                <h3 class="text-xs font-semibold text-slate-500 uppercase tracking-wider">공제 추정액 (약 10~15%)</h3>
                                <label class="text-[11px] text-slate-500 flex items-center gap-1 cursor-pointer">
                                    <input type="checkbox" id="enableTaxCalc" class="rounded text-govBlue" checked> 세금 차감 반영
                                </label>
                            </div>
                            <div class="flex justify-between items-center p-2.5 bg-slate-50 rounded-lg">
                                <span class="text-xs text-slate-600">소득세 + 지방소득세 (추정)</span>
                                <span id="deductIncomeTax" class="font-medium text-red-600 text-sm">-0 원</span>
                            </div>
                            <div class="flex justify-between items-center p-2.5 bg-slate-50 rounded-lg">
                                <span class="text-xs text-slate-600">건강보험 / 기여금 영향 추정</span>
                                <span id="deductHealthTax" class="font-medium text-red-600 text-sm">-0 원</span>
                            </div>
                            <div class="flex justify-between items-center p-2.5 bg-emerald-50 rounded-lg border border-emerald-100">
                                <span class="text-xs font-semibold text-emerald-900">실수령 예상액 (세후)</span>
                                <span id="breakdownNetPay" class="font-extrabold text-emerald-700 text-base">0 원</span>
                            </div>
                        </div>
                    </div>

                    <!-- Bottom Action Toolbar -->
                    <div class="pt-3 border-t border-slate-100 flex flex-wrap items-center justify-between gap-3 no-print">
                        <p class="text-[11px] text-slate-500">
                            * 본 예상금액은 실제 원천징수 세율 및 소속 기관의 지침에 따라 차이가 발생할 수 있습니다.
                        </p>
                        <button id="copySummaryBtn" class="text-xs bg-slate-800 hover:bg-slate-900 text-white px-4 py-2 rounded-lg transition flex items-center gap-1.5 shadow-sm">
                            <i class="fa-regular fa-copy"></i>
                            계산 결과 복사
                        </button>
                    </div>
                </div>

            </section>
        </div>
    </main>

    <!-- Footer -->
    <footer class="bg-slate-200 border-t border-slate-300 py-4 mt-8 text-center text-xs text-slate-600">
        <p>공무원 초과근무수당 계산기 &copy; 2026. 대한민국 공무원 수당 규정 기반 오차율 최소화 설계.</p>
    </footer>

    <!-- Notification Toast Modal -->
    <div id="toast" class="fixed bottom-5 right-5 bg-slate-900 text-white px-4 py-3 rounded-xl shadow-lg transform translate-y-20 opacity-0 transition-all duration-300 flex items-center gap-2 z-50 pointer-events-none">
        <i id="toastIcon" class="fa-solid fa-circle-check text-emerald-400"></i>
        <span id="toastMessage" class="text-xs font-medium">메시지 내용</span>
    </div>

    <!-- JavaScript Logic -->
    <script>
        /**
         * Standard Base Salary Standards for Public Servants (2026 Reference Tables)
         * Formula according to Regulations:
         * 시간외수당 단가 = (봉급기준액 × 0.55 × 1/209) × 1.5
         * 야간수당 단가 = (봉급기준액 × 0.55 × 1/209) × 0.5
         * 휴일수당 일액 = (봉급기준액 × 0.55 × 1/26) × 1.5
         */
        const BASE_SALARY_DATA = {
            general: {
                g9: 1780000,
                g8: 1980000,
                g7: 2260000,
                g6: 2650000,
                g5: 3120000
            },
            police: {
                g9: 1840000,
                g8: 2040000,
                g7: 2320000,
                g6: 2710000,
                g5: 3180000
            },
            teacher: {
                g9: 2100000,
                g8: 2300000,
                g7: 2550000,
                g6: 2900000,
                g5: 3350000
            }
        };

        // State Store
        let state = {
            jobType: 'general',
            jobGrade: 'g7',
            hourlyRate: 0,
            nightRate: 0,
            holidayRate: 0,
            isCustomRate: false,
            deductOneHour: true,
            maxMonthlyHours: 57,
            enableTax: true,
            logs: []
        };

        // DOM Elements
        const jobTypeSelect = document.getElementById('jobType');
        const jobGradeSelect = document.getElementById('jobGrade');
        const gradeContainer = document.getElementById('gradeContainer');
        const hourlyRateInput = document.getElementById('hourlyRateInput');
        const toggleRateLock = document.getElementById('toggleRateLock');
        const nightRateText = document.getElementById('nightRateText');
        const holidayRateText = document.getElementById('holidayRateText');
        const rateFormulaNotice = document.getElementById('rateFormulaNotice');
        
        const deductOneHourToggle = document.getElementById('deductOneHourToggle');
        const maxMonthlyHoursInput = document.getElementById('maxMonthlyHours');
        const enableTaxCalc = document.getElementById('enableTaxCalc');

        const workDateInput = document.getElementById('workDate');
        const workTypeSelect = document.getElementById('workType');
        const startTimeInput = document.getElementById('startTime');
        const endTimeInput = document.getElementById('endTime');
        const workMemoInput = document.getElementById('workMemo');
        const addLogBtn = document.getElementById('addLogBtn');
        const clearAllLogsBtn = document.getElementById('clearAllLogsBtn');

        const logTableBody = document.getElementById('logTableBody');
        const emptyRow = document.getElementById('emptyRow');
        const logCountBadge = document.getElementById('logCountBadge');

        // Summary Elements
        const summaryTotalHours = document.getElementById('summaryTotalHours');
        const summaryNightHours = document.getElementById('summaryNightHours');
        const summaryHolidayDays = document.getElementById('summaryHolidayDays');
        const summaryTotalPay = document.getElementById('summaryTotalPay');

        const breakdownOvertimePay = document.getElementById('breakdownOvertimePay');
        const breakdownNightPay = document.getElementById('breakdownNightPay');
        const breakdownHolidayPay = document.getElementById('breakdownHolidayPay');
        const breakdownGrossPay = document.getElementById('breakdownGrossPay');
        const deductIncomeTax = document.getElementById('deductIncomeTax');
        const deductHealthTax = document.getElementById('deductHealthTax');
        const breakdownNetPay = document.getElementById('breakdownNetPay');

        const copySummaryBtn = document.getElementById('copySummaryBtn');
        const resetAllBtn = document.getElementById('resetAllBtn');
        const printBtn = document.getElementById('printBtn');

        // Initialize App
        window.addEventListener('DOMContentLoaded', () => {
            // Set Default Date to Today
            const today = new Date().toISOString().split('T')[0];
            workDateInput.value = today;

            // Load from LocalStorage if available
            loadState();
            
            // Calculate Base Unit Rates
            calculateUnitRates();

            // Event Listeners
            initEventListeners();

            // Initial Render
            renderLogs();
        });

        function initEventListeners() {
            jobTypeSelect.addEventListener('change', (e) => {
                state.jobType = e.target.value;
                if (state.jobType === 'custom') {
                    gradeContainer.classList.add('hidden');
                    setCustomRateMode(true);
                } else {
                    gradeContainer.classList.remove('hidden');
                    setCustomRateMode(false);
                    calculateUnitRates();
                }
                saveState();
            });

            jobGradeSelect.addEventListener('change', (e) => {
                state.jobGrade = e.target.value;
                calculateUnitRates();
                saveState();
            });

            hourlyRateInput.addEventListener('input', (e) => {
                state.hourlyRate = parseInt(e.target.value) || 0;
                recalculateSecondaryRates();
                renderSummary();
                saveState();
            });

            toggleRateLock.addEventListener('click', () => {
                setCustomRateMode(hourlyRateInput.readOnly);
            });

            deductOneHourToggle.addEventListener('change', (e) => {
                state.deductOneHour = e.target.checked;
                recalculateLogs();
                saveState();
            });

            maxMonthlyHoursInput.addEventListener('input', (e) => {
                state.maxMonthlyHours = parseInt(e.target.value) || 0;
                renderSummary();
                saveState();
            });

            enableTaxCalc.addEventListener('change', (e) => {
                state.enableTax = e.target.checked;
                renderSummary();
                saveState();
            });

            addLogBtn.addEventListener('click', handleAddLog);
            clearAllLogsBtn.addEventListener('click', handleClearAllLogs);
            copySummaryBtn.addEventListener('click', handleCopySummary);
            resetAllBtn.addEventListener('click', handleResetAll);
            printBtn.addEventListener('click', () => window.print());
        }

        // Toggle Manual Hourly Rate Editing
        function setCustomRateMode(enableCustom) {
            state.isCustomRate = enableCustom;
            if (enableCustom) {
                hourlyRateInput.readOnly = false;
                hourlyRateInput.classList.remove('bg-slate-100');
                hourlyRateInput.classList.add('bg-white');
                toggleRateLock.innerHTML = `<i class="fa-solid fa-lock"></i> 자동 계산으로 변경`;
                rateFormulaNotice.innerText = '수동 입력 단가 적용 중';
            } else {
                hourlyRateInput.readOnly = true;
                hourlyRateInput.classList.remove('bg-white');
                hourlyRateInput.classList.add('bg-slate-100');
                toggleRateLock.innerHTML = `<i class="fa-solid fa-pen-to-square"></i> 직접 수정`;
                rateFormulaNotice.innerText = 'Formula: (봉급기준액 × 0.55 × 1/209) × 1.5';
                calculateUnitRates();
            }
        }

        // Calculate Standard Public Servant Rates
        function calculateUnitRates() {
            if (state.isCustomRate) return;

            const baseSalary = BASE_SALARY_DATA[state.jobType]?.[state.jobGrade] || 2260000;
            
            // Standard Official Formulas:
            // 시간외수당 단가 = (봉급기준액 * 0.55 * (1/209)) * 1.5
            const overtimeRate = Math.floor((baseSalary * 0.55 * (1 / 209)) * 1.5);
            
            state.hourlyRate = overtimeRate;
            hourlyRateInput.value = overtimeRate;

            recalculateSecondaryRates(baseSalary);
            renderSummary();
        }

        function recalculateSecondaryRates(baseSalaryRef) {
            let baseSalary = baseSalaryRef;
            if (!baseSalary) {
                // Infer base salary reverse or use hourly rate proportionally
                baseSalary = (state.hourlyRate / 1.5 / 0.55) * 209;
            }

            // 야간수당 단가 = (봉급기준액 * 0.55 * (1/209)) * 0.5
            state.nightRate = Math.floor((baseSalary * 0.55 * (1 / 209)) * 0.5);
            // 휴일수당 일액 = (봉급기준액 * 0.55 * (1/26)) * 1.5
            state.holidayRate = Math.floor((baseSalary * 0.55 * (1 / 26)) * 1.5);

            nightRateText.innerText = formatNumber(state.nightRate) + ' 원';
            holidayRateText.innerText = formatNumber(state.holidayRate) + ' 원';
        }

        // Add Log Item
        function handleAddLog() {
            const date = workDateInput.value;
            const type = workTypeSelect.value;
            const startTime = startTimeInput.value;
            const endTime = endTimeInput.value;
            const memo = workMemoInput.value.trim();

            if (!date || !startTime || !endTime) {
                showToast('날짜 및 근무 시간을 바르게 입력하세요.', 'error');
                return;
            }

            // Calculate hours & night hours
            const logData = calculateLogDetails(date, type, startTime, endTime, memo);
            
            state.logs.push(logData);
            // Sort by Date Descending
            state.logs.sort((a, b) => new Date(b.date) - new Date(a.date));

            // Clear Memo Input
            workMemoInput.value = '';

            renderLogs();
            saveState();
            showToast('근무 내역이 추가되었습니다.');
        }

        // Calculate Detail for single Log Entry
        function calculateLogDetails(date, type, startTime, endTime, memo) {
            const start = new Date(`${date}T${startTime}`);
            let end = new Date(`${date}T${endTime}`);

            // Handle Overnight shift (e.g. 22:00 to 02:00 next day)
            if (end <= start) {
                end.setDate(end.getDate() + 1);
            }

            // Total Hours worked in decimal
            const diffMs = end - start;
            const rawHours = Math.max(0, diffMs / (1000 * 60 * 60));

            let recognizedHours = rawHours;

            // Apply 1-Hour Deduction Rule for Weekday Overtime
            if (type === 'weekday' && state.deductOneHour) {
                recognizedHours = Math.max(0, rawHours - 1);
            }

            // Calculate Night Shift Hours (22:00 ~ 06:00)
            const nightHours = calculateNightHoursInRange(start, end);

            return {
                id: Date.now().toString(),
                date,
                type,
                startTime,
                endTime,
                memo,
                rawHours: parseFloat(rawHours.toFixed(1)),
                recognizedHours: parseFloat(recognizedHours.toFixed(1)),
                nightHours: parseFloat(nightHours.toFixed(1))
            };
        }

        // Helper: Calculate Night Work Hours (22:00 to 06:00)
        function calculateNightHoursInRange(startDate, endDate) {
            let nightMinutes = 0;
            let current = new Date(startDate.getTime());

            while (current < endDate) {
                const hour = current.getHours();
                // 22:00 ~ 23:59 or 00:00 ~ 05:59
                if (hour >= 22 || hour < 6) {
                    nightMinutes += 1;
                }
                current.setMinutes(current.getMinutes() + 1);
            }

            return nightMinutes / 60;
        }

        // Delete Log Item
        function deleteLog(id) {
            state.logs = state.logs.filter(log => log.id !== id);
            renderLogs();
            saveState();
            showToast('해당 근무 기록이 삭제되었습니다.');
        }

        function handleClearAllLogs() {
            if (state.logs.length === 0) return;
            if (confirm('전체 초과근무 내역을 삭제하시겠습니까?')) {
                state.logs = [];
                renderLogs();
                saveState();
                showToast('전체 내역이 초기화되었습니다.');
            }
        }

        // Recalculate all logs when rules change
        function recalculateLogs() {
            state.logs = state.logs.map(log => {
                return calculateLogDetails(log.date, log.type, log.startTime, log.endTime, log.memo);
            });
            renderLogs();
        }

        // Render Table & Summaries
        function renderLogs() {
            logTableBody.innerHTML = '';

            if (state.logs.length === 0) {
                logTableBody.appendChild(emptyRow);
                logCountBadge.innerText = '0건';
                renderSummary();
                return;
            }

            logCountBadge.innerText = `${state.logs.length}건`;

            state.logs.forEach(log => {
                const tr = document.createElement('tr');
                tr.className = 'hover:bg-slate-50 transition border-b border-slate-100';

                // Calculate estimated pay for this row
                let rowPay = 0;
                if (log.type === 'weekday') {
                    rowPay += log.recognizedHours * state.hourlyRate;
                } else {
                    // Holiday Allowance: Standard daily rate for holiday shift
                    rowPay += state.holidayRate;
                }
                rowPay += log.nightHours * state.nightRate;

                const typeBadge = log.type === 'weekday' 
                    ? `<span class="bg-blue-50 text-govBlue px-2 py-0.5 rounded font-medium text-xs">평일</span>`
                    : `<span class="bg-red-50 text-red-600 px-2 py-0.5 rounded font-medium text-xs">휴일</span>`;

                tr.innerHTML = `
                    <td class="p-2.5 font-medium text-slate-800">${log.date}</td>
                    <td class="p-2.5">${typeBadge}</td>
                    <td class="p-2.5 text-slate-600 text-xs">${log.startTime} ~ ${log.endTime}</td>
                    <td class="p-2.5 text-center">
                        <span class="text-slate-400 text-xs">${log.rawHours}h /</span>
                        <span class="font-bold text-slate-800">${log.recognizedHours}h</span>
                    </td>
                    <td class="p-2.5 text-center text-slate-700">${log.nightHours > 0 ? log.nightHours + 'h' : '-'}</td>
                    <td class="p-2.5 text-slate-500 max-w-[150px] truncate">${log.memo || '-'}</td>
                    <td class="p-2.5 text-right font-bold text-slate-800">${formatNumber(Math.floor(rowPay))}원</td>
                    <td class="p-2.5 text-center no-print">
                        <button onclick="deleteLog('${log.id}')" class="text-slate-400 hover:text-red-500 transition p-1">
                            <i class="fa-solid fa-xmark"></i>
                        </button>
                    </td>
                `;
                logTableBody.appendChild(tr);
            });

            renderSummary();
        }

        // Render Summary Totals & Pay Analysis
        function renderSummary() {
            let totalOvertimeHours = 0;
            let totalNightHours = 0;
            let totalHolidayDays = 0;

            let overtimePayTotal = 0;
            let nightPayTotal = 0;
            let holidayPayTotal = 0;

            state.logs.forEach(log => {
                totalNightHours += log.nightHours;

                if (log.type === 'weekday') {
                    totalOvertimeHours += log.recognizedHours;
                } else {
                    totalHolidayDays += 1;
                }
            });

            // Enforce Monthly Max Limit on Overtime Hours
            const cappedOvertimeHours = Math.min(totalOvertimeHours, state.maxMonthlyHours);

            overtimePayTotal = cappedOvertimeHours * state.hourlyRate;
            nightPayTotal = totalNightHours * state.nightRate;
            holidayPayTotal = totalHolidayDays * state.holidayRate;

            const totalGrossPay = overtimePayTotal + nightPayTotal + holidayPayTotal;

            // Estimate Tax / Deductions (~12% estimation)
            let estimatedTax = 0;
            let estimatedHealth = 0;

            if (state.enableTax) {
                estimatedTax = Math.floor(totalGrossPay * 0.08); // Income Tax ~8%
                estimatedHealth = Math.floor(totalGrossPay * 0.04); // Health Ins. ~4%
            }

            const totalNetPay = totalGrossPay - estimatedTax - estimatedHealth;

            // DOM Updates
            summaryTotalHours.innerText = `${cappedOvertimeHours} 시간` + (totalOvertimeHours > state.maxMonthlyHours ? ` (초과됨)` : '');
            summaryNightHours.innerText = `${totalNightHours} 시간`;
            summaryHolidayDays.innerText = `${totalHolidayDays} 일`;
            summaryTotalPay.innerText = `${formatNumber(Math.floor(totalGrossPay))} 원`;

            breakdownOvertimePay.innerText = `${formatNumber(Math.floor(overtimePayTotal))} 원`;
            breakdownNightPay.innerText = `${formatNumber(Math.floor(nightPayTotal))} 원`;
            breakdownHolidayPay.innerText = `${formatNumber(Math.floor(holidayPayTotal))} 원`;
            breakdownGrossPay.innerText = `${formatNumber(Math.floor(totalGrossPay))} 원`;

            deductIncomeTax.innerText = `-${formatNumber(estimatedTax)} 원`;
            deductHealthTax.innerText = `-${formatNumber(estimatedHealth)} 원`;
            breakdownNetPay.innerText = `${formatNumber(Math.floor(totalNetPay))} 원`;
        }

        // Copy Results Summary to Clipboard
        function handleCopySummary() {
            if (state.logs.length === 0) {
                showToast('복사할 초과근무 내역이 없습니다.', 'error');
                return;
            }

            const summaryText = `[공무원 초과근무수당 계산 결과]
• 직종/직급: ${jobTypeSelect.options[jobTypeSelect.selectedIndex].text} (${jobGradeSelect.options[jobGradeSelect.selectedIndex].text})
• 시간외수당 단가: ${formatNumber(state.hourlyRate)}원
• 총 인정 시간: ${summaryTotalHours.innerText}
• 야간근무 시간: ${summaryNightHours.innerText}
• 휴일근무 일수: ${summaryHolidayDays.innerText}
----------------------------------------
• 총 예상 수당 (세전): ${breakdownGrossPay.innerText}
• 실수령 예상액 (세후): ${breakdownNetPay.innerText}
----------------------------------------
* 본 내역은 공무원 수당 규정을 바탕으로 계산되었습니다.`;

            // Clipboard fallback
            if (navigator.clipboard && window.isSecureContext) {
                navigator.clipboard.writeText(summaryText).then(() => {
                    showToast('계산 결과 요약이 클립보드에 복사되었습니다.');
                });
            } else {
                const textArea = document.createElement("textarea");
                textArea.value = summaryText;
                document.body.appendChild(textArea);
                textArea.select();
                document.execCommand('copy');
                document.body.removeChild(textArea);
                showToast('계산 결과 요약이 클립보드에 복사되었습니다.');
            }
        }

        // Reset Everything
        function handleResetAll() {
            if (confirm('모든 설정 및 근무 기록을 초기화하시겠습니까?')) {
                localStorage.removeItem('gov_overtime_calculator_state');
                state.logs = [];
                state.jobType = 'general';
                state.jobGrade = 'g7';
                state.isCustomRate = false;
                jobTypeSelect.value = 'general';
                jobGradeSelect.value = 'g7';
                setCustomRateMode(false);
                calculateUnitRates();
                renderLogs();
                showToast('초기화되었습니다.');
            }
        }

        // Helper: Toast Notifications
        function showToast(message, type = 'success') {
            const toast = document.getElementById('toast');
            const toastMessage = document.getElementById('toastMessage');
            const toastIcon = document.getElementById('toastIcon');

            toastMessage.innerText = message;
            
            if (type === 'error') {
                toastIcon.className = 'fa-solid fa-circle-exclamation text-red-400';
            } else {
                toastIcon.className = 'fa-solid fa-circle-check text-emerald-400';
            }

            toast.classList.remove('translate-y-20', 'opacity-0');
            toast.classList.add('translate-y-0', 'opacity-100');

            setTimeout(() => {
                toast.classList.remove('translate-y-0', 'opacity-100');
                toast.classList.add('translate-y-20', 'opacity-0');
            }, 3000);
        }

        // Local Storage Helpers
        function saveState() {
            try {
                localStorage.setItem('gov_overtime_calculator_state', JSON.stringify(state));
            } catch (e) {
                console.error('Failed to save state to LocalStorage', e);
            }
        }

        function loadState() {
            try {
                const saved = localStorage.getItem('gov_overtime_calculator_state');
                if (saved) {
                    const parsed = JSON.parse(saved);
                    state = { ...state, ...parsed };

                    jobTypeSelect.value = state.jobType;
                    jobGradeSelect.value = state.jobGrade;
                    deductOneHourToggle.checked = state.deductOneHour;
                    maxMonthlyHoursInput.value = state.maxMonthlyHours;
                    enableTaxCalc.checked = state.enableTax;

                    if (state.jobType === 'custom') {
                        gradeContainer.classList.add('hidden');
                        setCustomRateMode(true);
                    }
                }
            } catch (e) {
                console.error('Failed to load state from LocalStorage', e);
            }
        }

        function formatNumber(num) {
            return num.toLocaleString('ko-KR');
        }
    </script>
</body>
</html>
