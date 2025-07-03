# my-mortgage-app
git init
git add .
git commit -m "Initial commit for mortgage loan app"
<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ระบบจัดการขายฝากที่ดิน/กู้ยืมเงิน</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Font Awesome for icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css">
    <style>
        /* Custom scrollbar for better aesthetics */
        ::-webkit-scrollbar {
            width: 8px;
            height: 8px;
        }
        ::-webkit-scrollbar-track {
            background: #f1f1f1;
            border-radius: 10px;
        }
        ::-webkit-scrollbar-thumb {
            background: #888;
            border-radius: 10px;
        }
        ::-webkit-scrollbar-thumb:hover {
            background: #555;
        }
        body {
            font-family: 'Inter', sans-serif;
            background-color: #f3f4f6; /* Light gray background */
        }
        .modal-overlay {
            background-color: rgba(0, 0, 0, 0.5);
            backdrop-filter: blur(5px);
        }
        .modal-content {
            animation: fadeInScale 0.3s ease-out;
        }
        @keyframes fadeInScale {
            from {
                opacity: 0;
                transform: scale(0.9);
            }
            to {
                opacity: 1;
                transform: scale(1);
            }
        }
        /* Status Badges */
        .status-active { background-color: #d1fae5; color: #065f46; } /* Green */
        .status-nearing-due { background-color: #fef9c3; color: #a16207; } /* Yellow */
        .status-overdue { background-color: #fee2e2; color: #991b1b; } /* Red */
        .status-matured { background-color: #e0f2fe; color: #0369a1; } /* Blue */
        .status-redeemed { background-color: #e5e7eb; color: #4b5563; } /* Gray */
    </style>
</head>
<body class="flex flex-col min-h-screen">
    <header class="bg-gradient-to-r from-blue-600 to-indigo-700 text-white p-4 shadow-lg">
        <div class="container mx-auto flex justify-between items-center">
            <h1 class="text-2xl font-bold">ระบบจัดการขายฝาก/กู้ยืม</h1>
            <nav>
                <ul class="flex space-x-4">
                    <li><button id="navDashboard" class="px-4 py-2 rounded-md hover:bg-blue-700 transition-colors text-white font-semibold">หน้าหลัก</button></li>
                    <li><button id="navContracts" class="px-4 py-2 rounded-md hover:bg-blue-700 transition-colors text-white font-semibold">จัดการสัญญา</button></li>
                </ul>
            </nav>
        </div>
    </header>

    <main class="flex-grow container mx-auto p-4 sm:p-6 lg:p-8">
        <!-- Dashboard Section -->
        <section id="dashboardSection" class="bg-white p-6 rounded-lg shadow-md mb-8">
            <h2 class="text-3xl font-bold text-gray-800 mb-6">ภาพรวมระบบ</h2>

            <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6 mb-8">
                <div class="bg-blue-100 p-5 rounded-lg shadow-sm flex items-center justify-between">
                    <div>
                        <p class="text-blue-700 text-lg font-semibold">จำนวนสัญญา</p>
                        <p id="totalContracts" class="text-blue-900 text-4xl font-bold mt-1">0</p>
                    </div>
                    <i class="fas fa-file-contract text-blue-500 text-5xl opacity-30"></i>
                </div>
                <div class="bg-green-100 p-5 rounded-lg shadow-sm flex items-center justify-between">
                    <div>
                        <p class="text-green-700 text-lg font-semibold">สัญญาที่ใช้งานอยู่</p>
                        <p id="activeContracts" class="text-green-900 text-4xl font-bold mt-1">0</p>
                    </div>
                    <i class="fas fa-check-circle text-green-500 text-5xl opacity-30"></i>
                </div>
                <div class="bg-yellow-100 p-5 rounded-lg shadow-sm flex items-center justify-between">
                    <div>
                        <p class="text-yellow-700 text-lg font-semibold">สัญญาเกินกำหนด</p>
                        <p id="overdueContracts" class="text-yellow-900 text-4xl font-bold mt-1">0</p>
                    </div>
                    <i class="fas fa-exclamation-triangle text-yellow-500 text-5xl opacity-30"></i>
                </div>
                <div class="bg-purple-100 p-5 rounded-lg shadow-sm flex items-center justify-between">
                    <div>
                        <p class="text-purple-700 text-lg font-semibold">เงินต้นรวมลงทุน</p>
                        <p id="totalInvested" class="text-purple-900 text-4xl font-bold mt-1">0.00 ฿</p>
                    </div>
                    <i class="fas fa-hand-holding-usd text-purple-500 text-5xl opacity-30"></i>
                </div>
            </div>

            <h3 class="text-2xl font-bold text-gray-800 mb-4">การแจ้งเตือน</h3>
            <div id="alertList" class="space-y-4">
                <!-- Alerts will be dynamically loaded here -->
                <div class="bg-blue-50 border-l-4 border-blue-400 text-blue-700 p-4 rounded-md shadow-sm" role="alert">
                    <p class="font-bold">ไม่มีการแจ้งเตือนในขณะนี้</p>
                    <p class="text-sm">ข้อมูลล่าสุด ณ ปัจจุบัน</p>
                </div>
            </div>
        </section>

        <!-- Contracts Section -->
        <section id="contractsSection" class="bg-white p-6 rounded-lg shadow-md hidden">
            <div class="flex flex-col sm:flex-row justify-between items-center mb-6">
                <h2 class="text-3xl font-bold text-gray-800 mb-4 sm:mb-0">จัดการสัญญา</h2>
                <button id="addContractBtn" class="bg-blue-600 hover:bg-blue-700 text-white px-6 py-3 rounded-md shadow-lg transition-all duration-300 transform hover:scale-105">
                    <i class="fas fa-plus-circle mr-2"></i> เพิ่มสัญญาใหม่
                </button>
            </div>

            <div class="mb-6 grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-4">
                <input type="text" id="searchContract" placeholder="ค้นหาด้วยชื่อ, เลขโฉนด, หรือเบอร์โทร" class="p-3 border border-gray-300 rounded-md focus:ring-blue-500 focus:border-blue-500 w-full">
                <select id="filterStatus" class="p-3 border border-gray-300 rounded-md focus:ring-blue-500 focus:border-blue-500 w-full">
                    <option value="">ทั้งหมด (สถานะ)</option>
                    <option value="Active">ใช้งานอยู่</option>
                    <option value="Nearing Due">ใกล้ครบกำหนด</option>
                    <option value="Overdue">เกินกำหนดชำระ</option>
                    <option value="Matured">ครบกำหนดไถ่ถอน/คืนเงิน</option>
                    <option value="Redeemed">ไถ่ถอนแล้ว/ชำระคืนแล้ว</option>
                </select>
                <select id="filterType" class="p-3 border border-gray-300 rounded-md focus:ring-blue-500 focus:border-blue-500 w-full">
                    <option value="">ทั้งหมด (ประเภท)</option>
                    <option value="ขายฝาก">ขายฝาก</option>
                    <option value="กู้ยืม">กู้ยืม</option>
                </select>
            </div>

            <div class="overflow-x-auto rounded-lg shadow-md">
                <table class="min-w-full bg-white border-collapse">
                    <thead class="bg-gray-100">
                        <tr>
                            <th class="py-3 px-4 text-left text-sm font-semibold text-gray-600 uppercase">ชื่อลูกหนี้/ผู้ขายฝาก</th>
                            <th class="py-3 px-4 text-left text-sm font-semibold text-gray-600 uppercase">ประเภท</th>
                            <th class="py-3 px-4 text-left text-sm font-semibold text-gray-600 uppercase">เงินต้น</th>
                            <th class="py-3 px-4 text-left text-sm font-semibold text-gray-600 uppercase">กำหนดชำระถัดไป</th>
                            <th class="py-3 px-4 text-left text-sm font-semibold text-gray-600 uppercase">สถานะ</th>
                            <th class="py-3 px-4 text-left text-sm font-semibold text-gray-600 uppercase">การดำเนินการ</th>
                        </tr>
                    </thead>
                    <tbody id="contractsTableBody">
                        <!-- Contract rows will be dynamically loaded here -->
                        <tr>
                            <td colspan="6" class="text-center py-4 text-gray-500">ไม่มีข้อมูลสัญญา</td>
                        </tr>
                    </tbody>
                </table>
            </div>
        </section>
    </main>

    <!-- Add/Edit Contract Modal -->
    <div id="contractModal" class="fixed inset-0 flex items-center justify-center z-50 hidden modal-overlay">
        <div class="bg-white p-8 rounded-lg shadow-xl w-11/12 md:w-2/3 lg:w-1/2 max-h-[90vh] overflow-y-auto modal-content">
            <h3 id="modalTitle" class="text-2xl font-bold text-gray-800 mb-6">เพิ่มสัญญาใหม่</h3>
            <form id="contractForm" class="grid grid-cols-1 md:grid-cols-2 gap-6">
                <input type="hidden" id="contractId">

                <!-- Borrower/Seller Info -->
                <div class="md:col-span-2 text-lg font-semibold text-gray-700">ข้อมูลลูกหนี้/ผู้ขายฝาก</div>
                <div>
                    <label for="borrowerName" class="block text-sm font-medium text-gray-700 mb-1">ชื่อ-นามสกุล</label>
                    <input type="text" id="borrowerName" required class="w-full p-3 border border-gray-300 rounded-md focus:ring-blue-500 focus:border-blue-500">
                </div>
                <div>
                    <label for="phoneNumber" class="block text-sm font-medium text-gray-700 mb-1">เบอร์โทรศัพท์</label>
                    <input type="tel" id="phoneNumber" maxlength="10" pattern="[0-9]{10}" title="กรุณากรอกเบอร์โทรศัพท์ 10 หลัก" class="w-full p-3 border border-gray-300 rounded-md focus:ring-blue-500 focus:border-blue-500">
                </div>
                <div>
                    <label for="idCard" class="block text-sm font-medium text-gray-700 mb-1">เลขบัตรประชาชน</label>
                    <input type="text" id="idCard" maxlength="13" pattern="[0-9]{13}" title="กรุณากรอกเลขบัตรประชาชน 13 หลัก" class="w-full p-3 border border-gray-300 rounded-md focus:ring-blue-500 focus:border-blue-500">
                </div>
                <div>
                    <label for="address" class="block text-sm font-medium text-gray-700 mb-1">ที่อยู่</label>
                    <input type="text" id="address" class="w-full p-3 border border-gray-300 rounded-md focus:ring-blue-500 focus:border-blue-500">
                </div>
                <!-- New: Broker Name -->
                <div>
                    <label for="brokerName" class="block text-sm font-medium text-gray-700 mb-1">ชื่อนายหน้า</label>
                    <input type="text" id="brokerName" class="w-full p-3 border border-gray-300 rounded-md focus:ring-blue-500 focus:border-blue-500">
                </div>
                <!-- End New: Broker Name -->

                <!-- Has Property Selection -->
                <div class="md:col-span-2">
                    <label class="block text-sm font-medium text-gray-700 mb-1">มีทรัพย์สิน หรือไม่มีทรัพย์สิน</label>
                    <div class="flex items-center space-x-4">
                        <label class="inline-flex items-center">
                            <input type="radio" name="hasProperty" value="true" id="hasPropertyYes" class="form-radio text-blue-600 h-5 w-5">
                            <span class="ml-2 text-gray-700">มีทรัพย์สิน</span>
                        </label>
                        <label class="inline-flex items-center">
                            <input type="radio" name="hasProperty" value="false" id="hasPropertyNo" class="form-radio text-blue-600 h-5 w-5" checked>
                            <span class="ml-2 text-gray-700">ไม่มีทรัพย์สิน</span>
                        </label>
                    </div>
                </div>

                <!-- Contract Details -->
                <div class="md:col-span-2 text-lg font-semibold text-gray-700 mt-4">รายละเอียดสัญญา</div>
                <div>
                    <label for="contractType" class="block text-sm font-medium text-gray-700 mb-1">ประเภทสัญญา</label>
                    <select id="contractType" required class="w-full p-3 border border-gray-300 rounded-md focus:ring-blue-500 focus:border-blue-500">
                        <option value="ขายฝาก">ขายฝาก</option>
                        <option value="กู้ยืม">กู้ยืม</option>
                    </select>
                </div>
                <div>
                    <label for="principalAmount" class="block text-sm font-medium text-gray-700 mb-1">เงินต้น (฿)</label>
                    <input type="number" id="principalAmount" step="0.01" required class="w-full p-3 border border-gray-300 rounded-md focus:ring-blue-500 focus:border-blue-500">
                </div>
                <div>
                    <label for="interestRate" class="block text-sm font-medium text-gray-700 mb-1">อัตราดอกเบี้ย (% ต่อเดือน)</label>
                    <input type="number" id="interestRate" step="0.01" required class="w-full p-3 border border-gray-300 rounded-md focus:ring-blue-500 focus:border-blue-500">
                </div>
                <div>
                    <label for="startDate" class="block text-sm font-medium text-gray-700 mb-1">วันที่ทำสัญญา</label>
                    <input type="date" id="startDate" required class="w-full p-3 border border-gray-300 rounded-md focus:ring-blue-500 focus:border-blue-500">
                </div>
                <div>
                    <label for="endDate" class="block text-sm font-medium text-gray-700 mb-1">วันที่ครบกำหนด</label>
                    <input type="date" id="endDate" required class="w-full p-3 border border-gray-300 rounded-md focus:ring-blue-500 focus:border-blue-500">
                </div>
                <div>
                    <label for="paymentFrequency" class="block text-sm font-medium text-gray-700 mb-1">ความถี่การชำระ</label>
                    <select id="paymentFrequency" class="w-full p-3 border border-gray-300 rounded-md focus:ring-blue-500 focus:border-blue-500">
                        <option value="monthly">รายเดือน</option>
                        <option value="quarterly">ราย 3 เดือน</option>
                        <option value="annually">รายปี</option>
                    </select>
                </div>
                <!-- New: Manual Next Payment Due Date -->
                <div>
                    <label for="nextPaymentDueDateManual" class="block text-sm font-medium text-gray-700 mb-1">กำหนดชำระถัดไป (ระบุเอง)</label>
                    <input type="date" id="nextPaymentDueDateManual" class="w-full p-3 border border-gray-300 rounded-md focus:ring-blue-500 focus:border-blue-500">
                </div>
                <!-- End New: Manual Next Payment Due Date -->

                <!-- Property Details (Conditional for ขายฝาก and hasProperty) -->
                <div id="propertyDetailsSection" class="md:col-span-2 hidden">
                    <div class="text-lg font-semibold text-gray-700 mt-4">รายละเอียดทรัพย์สิน (สำหรับขายฝาก)</div>
                    <div>
                        <label for="propertyType" class="block text-sm font-medium text-gray-700 mb-1">ประเภททรัพย์สิน</label>
                        <input type="text" id="propertyType" class="w-full p-3 border border-gray-300 rounded-md focus:ring-blue-500 focus:border-blue-500" placeholder="เช่น ที่ดินเปล่า, บ้านพร้อมที่ดิน">
                    </div>
                    <div>
                        <label for="landDeedNo" class="block text-sm font-medium text-gray-700 mb-1">เลขโฉนด/เลขที่ดิน</label>
                        <input type="text" id="landDeedNo" class="w-full p-3 border border-gray-300 rounded-md focus:ring-blue-500 focus:border-blue-500">
                    </div>
                    <div>
                        <label for="propertySize" class="block text-sm font-medium text-gray-700 mb-1">ขนาด (เช่น ไร่-งาน-วา)</label>
                        <input type="text" id="propertySize" class="w-full p-3 border border-gray-300 rounded-md focus:ring-blue-500 focus:border-blue-500">
                    </div>
                    <div>
                        <label for="propertyLocation" class="block text-sm font-medium text-gray-700 mb-1">ที่ตั้ง</label>
                        <input type="text" id="propertyLocation" class="w-full p-3 border border-gray-300 rounded-md focus:ring-blue-500 focus:border-blue-500">
                    </div>

                    <!-- Property Related Costs -->
                    <div class="md:col-span-2 text-lg font-semibold text-gray-700 mt-4">ต้นทุนที่เกี่ยวข้องกับทรัพย์สิน</div>
                    <div>
                        <label for="transferFee" class="block text-sm font-medium text-gray-700 mb-1">ค่าหักโอน (฿)</label>
                        <input type="number" id="transferFee" step="0.01" value="0" class="w-full p-3 border border-gray-300 rounded-md focus:ring-blue-500 focus:border-blue-500">
                    </div>
                    <div>
                        <label for="landDeptServiceFee" class="block text-sm font-medium text-gray-700 mb-1">ค่าบริการพนักงานกรมที่ดิน (฿)</label>
                        <input type="number" id="landDeptServiceFee" step="0.01" value="0" class="w-full p-3 border border-gray-300 rounded-md focus:ring-blue-500 focus:border-blue-500">
                    </div>
                    <div>
                        <label for="brokerageFee" class="block text-sm font-medium text-gray-700 mb-1">ค่านายหน้า (฿)</label>
                        <input type="number" id="brokerageFee" step="0.01" value="0" class="w-full p-3 border border-gray-300 rounded-md focus:ring-blue-500 focus:border-blue-500">
                    </div>
                    <!-- New: Prepaid Interest Months -->
                    <div>
                        <label for="prepaidInterestMonths" class="block text-sm font-medium text-gray-700 mb-1">เก็บอัตราดอกเบี้ยล่วงหน้า</label>
                        <select id="prepaidInterestMonths" class="w-full p-3 border border-gray-300 rounded-md focus:ring-blue-500 focus:border-blue-500">
                            <option value="0">ไม่เก็บล่วงหน้า</option>
                            <option value="1">เก็บล่วงหน้า 1 เดือน</option>
                            <option value="2">เก็บล่วงหน้า 2 เดือน</option>
                            <option value="3">เก็บล่วงหน้า 3 เดือน</option>
                        </select>
                    </div>
                    <!-- End New: Prepaid Interest Months -->

                    <!-- New: Actual Principal Display -->
                    <div class="md:col-span-2 bg-blue-50 p-4 rounded-md border border-blue-200">
                        <p class="text-blue-800 text-lg font-semibold">ต้นทุนที่แท้จริงของสัญญา:</p>
                        <p id="actualPrincipalDisplay" class="text-blue-900 text-3xl font-bold mt-1">0.00 ฿</p>
                    </div>
                    <!-- End New: Actual Principal Display -->
                </div>

                <div class="md:col-span-2 flex justify-end space-x-4 mt-6">
                    <button type="button" id="cancelContractModalBtn" class="bg-gray-300 hover:bg-gray-400 text-gray-800 px-6 py-3 rounded-md shadow-sm transition-colors">ยกเลิก</button>
                    <button type="submit" class="bg-blue-600 hover:bg-blue-700 text-white px-6 py-3 rounded-md shadow-lg transition-all duration-300 transform hover:scale-105">บันทึกสัญญา</button>
                </div>
            </form>
        </div>
    </div>

    <!-- Payment History Modal -->
    <div id="paymentHistoryModal" class="fixed inset-0 flex items-center justify-center z-50 hidden modal-overlay">
        <div class="bg-white p-8 rounded-lg shadow-xl w-11/12 md:w-2/3 lg:w-1/2 max-h-[90vh] overflow-y-auto modal-content">
            <h3 class="text-2xl font-bold text-gray-800 mb-6">ประวัติการชำระดอกเบี้ย</h3>
            <p class="text-gray-600 mb-4">สัญญา: <span id="paymentModalContractName" class="font-semibold"></span></p>
            <p class="text-gray-600 mb-4">ดอกเบี้ยต่อเดือน: <span id="monthlyInterestAmount" class="font-semibold"></span> ฿</p>

            <div class="mb-6">
                <label for="paymentDate" class="block text-sm font-medium text-gray-700 mb-1">วันที่ชำระ</label>
                <input type="date" id="paymentDate" class="w-full p-3 border border-gray-300 rounded-md focus:ring-blue-500 focus:border-blue-500 mb-3">
                <label for="paymentAmount" class="block text-sm font-medium text-gray-700 mb-1">จำนวนเงินที่ชำระ (฿)</label>
                <input type="number" id="paymentAmount" step="0.01" class="w-full p-3 border border-gray-300 rounded-md focus:ring-blue-500 focus:border-blue-500 mb-3">
                <button id="addPaymentBtn" class="bg-green-600 hover:bg-green-700 text-white px-5 py-2 rounded-md shadow-sm transition-colors w-full">
                    <i class="fas fa-plus-circle mr-2"></i> บันทึกการชำระ
                </button>
            </div>

            <div class="overflow-x-auto rounded-lg shadow-md">
                <table class="min-w-full bg-white border-collapse">
                    <thead class="bg-gray-100">
                        <tr>
                            <th class="py-3 px-4 text-left text-sm font-semibold text-gray-600 uppercase">วันที่ชำระ</th>
                            <th class="py-3 px-4 text-left text-sm font-semibold text-gray-600 uppercase">จำนวนเงิน (฿)</th>
                            <th class="py-3 px-4 text-left text-sm font-semibold text-gray-600 uppercase">การดำเนินการ</th>
                        </tr>
                    </thead>
                    <tbody id="paymentHistoryTableBody">
                        <!-- Payment history will be dynamically loaded here -->
                        <tr>
                            <td colspan="3" class="text-center py-4 text-gray-500">ยังไม่มีประวัติการชำระ</td>
                        </tr>
                    </tbody>
                </table>
            </div>

            <div class="flex justify-end mt-6">
                <button type="button" id="closePaymentHistoryModalBtn" class="bg-gray-300 hover:bg-gray-400 text-gray-800 px-6 py-3 rounded-md shadow-sm transition-colors">ปิด</button>
            </div>
        </div>
    </div>

    <!-- Confirmation Modal -->
    <div id="confirmationModal" class="fixed inset-0 flex items-center justify-center z-50 hidden modal-overlay">
        <div class="bg-white p-8 rounded-lg shadow-xl w-11/12 md:w-1/3 modal-content">
            <h3 class="text-xl font-bold text-gray-800 mb-4" id="confirmModalTitle">ยืนยันการลบ</h3>
            <p class="text-gray-700 mb-6" id="confirmModalMessage">คุณแน่ใจหรือไม่ที่ต้องการลบรายการนี้?</p>
            <div class="flex justify-end space-x-4">
                <button type="button" id="cancelConfirmBtn" class="bg-gray-300 hover:bg-gray-400 text-gray-800 px-6 py-3 rounded-md shadow-sm transition-colors">ยกเลิก</button>
                <button type="button" id="confirmActionBtn" class="bg-red-600 hover:bg-red-700 text-white px-6 py-3 rounded-md shadow-lg transition-all duration-300 transform hover:scale-105">ยืนยัน</button>
            </div>
        </div>
    </div>

    <!-- Message Box (for alerts/notifications) -->
    <div id="messageBox" class="fixed bottom-4 right-4 bg-blue-600 text-white px-6 py-3 rounded-md shadow-lg hidden z-50 transition-transform transform translate-y-full duration-300 ease-out">
        <p id="messageBoxText"></p>
    </div>

    <footer class="bg-gray-800 text-white p-4 text-center mt-8">
        <div class="container mx-auto">
            <p>&copy; 2024 ระบบจัดการขายฝาก/กู้ยืม. สงวนลิขสิทธิ์.</p>
        </div>
    </footer>

    <script>
        // Global variables for contracts data
        let contracts = [];
        let currentContractIdForPayment = null; // To keep track of which contract's payments are being viewed

        // DOM Elements
        const dashboardSection = document.getElementById('dashboardSection');
        const contractsSection = document.getElementById('contractsSection');
        const navDashboard = document.getElementById('navDashboard');
        const navContracts = document.getElementById('navContracts');

        const totalContractsDisplay = document.getElementById('totalContracts');
        const activeContractsDisplay = document.getElementById('activeContracts');
        const overdueContractsDisplay = document.getElementById('overdueContracts');
        const totalInvestedDisplay = document.getElementById('totalInvested');
        const alertList = document.getElementById('alertList');

        const addContractBtn = document.getElementById('addContractBtn');
        const contractsTableBody = document.getElementById('contractsTableBody');
        const searchContract = document.getElementById('searchContract');
        const filterStatus = document.getElementById('filterStatus');
        const filterType = document.getElementById('filterType');

        const contractModal = document.getElementById('contractModal');
        const modalTitle = document.getElementById('modalTitle');
        const contractForm = document.getElementById('contractForm');
        const contractIdInput = document.getElementById('contractId');
        const borrowerNameInput = document.getElementById('borrowerName');
        const phoneNumberInput = document.getElementById('phoneNumber');
        const idCardInput = document.getElementById('idCard');
        const addressInput = document.getElementById('address');
        const brokerNameInput = document.getElementById('brokerName');
        const hasPropertyYes = document.getElementById('hasPropertyYes');
        const hasPropertyNo = document.getElementById('hasPropertyNo');
        const contractTypeInput = document.getElementById('contractType');
        const principalAmountInput = document.getElementById('principalAmount');
        const interestRateInput = document.getElementById('interestRate');
        const startDateInput = document.getElementById('startDate');
        const endDateInput = document.getElementById('endDate');
        const paymentFrequencyInput = document.getElementById('paymentFrequency');
        const nextPaymentDueDateManualInput = document.getElementById('nextPaymentDueDateManual'); // New DOM element
        const propertyDetailsSection = document.getElementById('propertyDetailsSection');
        const propertyTypeInput = document.getElementById('propertyType');
        const landDeedNoInput = document.getElementById('landDeedNo');
        const propertySizeInput = document.getElementById('propertySize');
        const propertyLocationInput = document.getElementById('propertyLocation');
        const transferFeeInput = document.getElementById('transferFee');
        const landDeptServiceFeeInput = document.getElementById('landDeptServiceFee');
        const brokerageFeeInput = document.getElementById('brokerageFee');
        const prepaidInterestMonthsInput = document.getElementById('prepaidInterestMonths');
        const actualPrincipalDisplay = document.getElementById('actualPrincipalDisplay');
        const cancelContractModalBtn = document.getElementById('cancelContractModalBtn');

        const paymentHistoryModal = document.getElementById('paymentHistoryModal');
        const paymentModalContractName = document.getElementById('paymentModalContractName');
        const monthlyInterestAmountDisplay = document.getElementById('monthlyInterestAmount');
        const paymentDateInput = document.getElementById('paymentDate');
        const paymentAmountInput = document.getElementById('paymentAmount');
        const addPaymentBtn = document.getElementById('addPaymentBtn');
        const paymentHistoryTableBody = document.getElementById('paymentHistoryTableBody');
        const closePaymentHistoryModalBtn = document.getElementById('closePaymentHistoryModalBtn');

        const confirmationModal = document.getElementById('confirmationModal');
        const confirmModalTitle = document.getElementById('confirmModalTitle');
        const confirmModalMessage = document.getElementById('confirmModalMessage');
        const cancelConfirmBtn = document.getElementById('cancelConfirmBtn');
        const confirmActionBtn = document.getElementById('confirmActionBtn');

        const messageBox = document.getElementById('messageBox');
        const messageBoxText = document.getElementById('messageBoxText');

        // --- Utility Functions ---

        /**
         * Generates a unique ID (UUID v4).
         * @returns {string} A unique ID.
         */
        function generateUUID() {
            return 'xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx'.replace(/[xy]/g, function(c) {
                var r = Math.random() * 16 | 0,
                    v = c == 'x' ? r : (r & 0x3 | 0x8);
                return v.toString(16);
            });
        }

        /**
         * Formats a number as Thai Baht currency.
         * @param {number} amount - The amount to format.
         * @returns {string} Formatted currency string.
         */
        function formatCurrency(amount) {
            return new Intl.NumberFormat('th-TH', {
                style: 'currency',
                currency: 'THB',
                minimumFractionDigits: 2
            }).format(amount);
        }

        /**
         * Formats a date string to a readable Thai format.
         * @param {string} dateString - The date string (e.g., "YYYY-MM-DD").
         * @returns {string} Formatted date string.
         */
        function formatDate(dateString) {
            if (!dateString) return '-';
            const options = {
                year: 'numeric',
                month: 'long',
                day: 'numeric'
            };
            return new Date(dateString).toLocaleDateString('th-TH', options);
        }

        /**
         * Shows a temporary message box.
         * @param {string} message - The message to display.
         * @param {string} type - 'success', 'error', 'info'.
         */
        function showMessageBox(message, type = 'info') {
            messageBoxText.textContent = message;
            messageBox.classList.remove('hidden', 'bg-green-600', 'bg-red-600', 'bg-blue-600');
            messageBox.classList.add('translate-y-0');

            if (type === 'success') {
                messageBox.classList.add('bg-green-600');
            } else if (type === 'error') {
                messageBox.classList.add('bg-red-600');
            } else {
                messageBox.classList.add('bg-blue-600');
            }

            setTimeout(() => {
                messageBox.classList.remove('translate-y-0');
                messageBox.classList.add('translate-y-full');
                setTimeout(() => {
                    messageBox.classList.add('hidden');
                }, 300); // Allow transition to complete before hiding
            }, 3000);
        }

        // --- Data Management (LocalStorage) ---

        /**
         * Saves the current contracts array to LocalStorage.
         */
        function saveContracts() {
            try {
                localStorage.setItem('mortgageLoanContracts', JSON.stringify(contracts));
                console.log('Contracts saved to LocalStorage.');
            } catch (e) {
                console.error('Error saving to LocalStorage:', e);
                showMessageBox('ข้อผิดพลาดในการบันทึกข้อมูล', 'error');
            }
        }

        /**
         * Loads contracts data from LocalStorage.
         */
        function loadContracts() {
            try {
                const storedData = localStorage.getItem('mortgageLoanContracts');
                contracts = storedData ? JSON.parse(storedData) : [];
                console.log('Contracts loaded from LocalStorage.');
            } catch (e) {
                console.error('Error loading from LocalStorage:', e);
                contracts = []; // Reset if data is corrupted
                showMessageBox('ข้อผิดพลาดในการโหลดข้อมูล อาจเป็นข้อมูลเก่าเสียหาย', 'error');
            }
        }

        // --- Contract Status and Calculations ---

        /**
         * Calculates the monthly interest amount for a contract.
         * @param {object} contract - The contract object.
         * @returns {number} Monthly interest amount.
         */
        function calculateMonthlyInterest(contract) {
            // Ensure principalAmount and interestRate are numbers
            const principal = parseFloat(contract.principalAmount);
            const interest = parseFloat(contract.interestRate);
            if (isNaN(principal) || isNaN(interest) || principal <= 0 || interest < 0) {
                return 0;
            }
            return (principal * interest) / 100;
        }

        /**
         * Determines the next payment due date based on contract start date and frequency.
         * This function calculates the *next scheduled* payment date based on the contract's start date,
         * regardless of actual payment history. It finds the first scheduled payment date that is
         * on or after the current date.
         * @param {object} contract - The contract object.
         * @returns {string|null} The next due date in ISO format (YYYY-MM-DD), or null if no more payments are due.
         */
        function getNextPaymentDueDate(contract) {
            // This function is now primarily for internal status calculation consistency
            // and not directly for display in the main table.
            const today = new Date();
            today.setHours(0, 0, 0, 0); // Normalize to start of day

            const startDate = new Date(contract.startDate);
            startDate.setHours(0, 0, 0, 0);

            const endDate = new Date(contract.endDate);
            endDate.setHours(0, 0, 0, 0);

            let currentCalculatedDueDate = new Date(startDate);

            // If the start date itself is after the end date, or contract is already matured
            if (startDate > endDate || today > endDate) {
                return null;
            }

            // Advance the currentCalculatedDueDate by payment frequency until it's today or in the future
            // or until it exceeds the contract end date.
            let i = 0;
            const maxIterations = 1000; // Safety break to prevent infinite loops for very long contracts
            while (currentCalculatedDueDate < today && i < maxIterations) {
                if (contract.paymentFrequency === 'monthly') {
                    currentCalculatedDueDate.setMonth(currentCalculatedDueDate.getMonth() + 1);
                } else if (contract.paymentFrequency === 'quarterly') {
                    currentCalculatedDueDate.setMonth(currentCalculatedDueDate.getMonth() + 3);
                } else if (contract.paymentFrequency === 'annually') {
                    currentCalculatedDueDate.setFullYear(currentCalculatedDueDate.getFullYear() + 1);
                }

                // If the calculated date goes past the end date, no more payments are due.
                if (currentCalculatedDueDate > endDate) {
                    return null;
                }
                i++;
            }

            // After the loop, currentCalculatedDueDate holds the first scheduled due date
            // that is on or after 'today'.
            // If it somehow went past the end date during the last iteration, return null.
            if (currentCalculatedDueDate > endDate) {
                return null;
            }

            return currentCalculatedDueDate.toISOString().split('T')[0];
        }


        /**
         * Updates the status of all contracts based on current date and payment history.
         */
        function updateContractStatuses() {
            const today = new Date();
            today.setHours(0, 0, 0, 0); // Normalize to start of day

            contracts.forEach(contract => {
                const endDate = new Date(contract.endDate);
                endDate.setHours(0, 0, 0, 0);

                if (contract.status === 'Redeemed') {
                    return; // Don't change status if already redeemed
                }

                // Use the manually entered next payment due date for status calculation
                const nextDueDateForStatus = contract.nextPaymentDueDateManual;

                if (today >= endDate) {
                    contract.status = 'Matured';
                } else if (nextDueDateForStatus) {
                    const nextDueDateTimeForStatus = new Date(nextDueDateForStatus);
                    nextDueDateTimeForStatus.setHours(0, 0, 0, 0);

                    // Check for Overdue
                    if (today > nextDueDateTimeForStatus) {
                        contract.status = 'Overdue';
                    }
                    // Check for Nearing Due (e.g., within 7 days)
                    else if (nextDueDateTimeForStatus.getTime() - today.getTime() <= (7 * 24 * 60 * 60 * 1000)) {
                        contract.status = 'Nearing Due';
                    }
                    // Otherwise, it's Active
                    else {
                        contract.status = 'Active';
                    }
                } else {
                    // If no manual next payment due date is provided, default to 'Active'
                    // unless the contract end date is already passed.
                    if (today < endDate) {
                         contract.status = 'Active';
                    } else {
                        contract.status = 'Matured';
                    }
                }
            });
            saveContracts(); // Save updated statuses
        }


        // --- UI Rendering ---

        /**
         * Renders the dashboard with summary statistics and alerts.
         */
        function renderDashboard() {
            updateContractStatuses(); // Ensure statuses are up-to-date
            const activeCount = contracts.filter(c => c.status === 'Active' || c.status === 'Nearing Due').length;
            const overdueCount = contracts.filter(c => c.status === 'Overdue').length;
            const totalInvested = contracts.reduce((sum, c) => sum + parseFloat(c.principalAmount), 0);

            totalContractsDisplay.textContent = contracts.length;
            activeContractsDisplay.textContent = activeCount;
            overdueContractsDisplay.textContent = overdueCount;
            totalInvestedDisplay.textContent = formatCurrency(totalInvested);

            alertList.innerHTML = '';
            let hasAlerts = false;

            contracts.forEach(contract => {
                const today = new Date();
                today.setHours(0, 0, 0, 0);

                const endDate = new Date(contract.endDate);
                endDate.setHours(0, 0, 0, 0);

                // Use the manually entered next payment due date for alerts
                const displayNextDueDateForAlert = contract.nextPaymentDueDateManual;
                const nextDueDateTimeForAlert = displayNextDueDateForAlert ? new Date(displayNextDueDateForAlert) : null;
                if (nextDueDateTimeForAlert) nextDueDateTimeForAlert.setHours(0, 0, 0, 0);

                let alertMessage = '';
                let alertClass = '';
                let iconClass = '';

                if (contract.status === 'Overdue') {
                    alertMessage = `สัญญาของ ${contract.borrowerName} (เงินต้น ${formatCurrency(contract.principalAmount)}) เกินกำหนดชำระดอกเบี้ยแล้ว`;
                    alertClass = 'bg-red-50 border-red-400 text-red-700';
                    iconClass = 'fas fa-exclamation-circle text-red-500';
                    hasAlerts = true;
                } else if (contract.status === 'Nearing Due' && displayNextDueDateForAlert) { // Ensure manual date exists for this alert
                    alertMessage = `สัญญาของ ${contract.borrowerName} (เงินต้น ${formatCurrency(contract.principalAmount)}) ใกล้ถึงกำหนดชำระดอกเบี้ยถัดไป: ${formatDate(displayNextDueDateForAlert)}`;
                    alertClass = 'bg-yellow-50 border-yellow-400 text-yellow-700';
                    iconClass = 'fas fa-bell text-yellow-500';
                    hasAlerts = true;
                } else if (contract.status === 'Matured') {
                    alertMessage = `สัญญาของ ${contract.borrowerName} (เงินต้น ${formatCurrency(contract.principalAmount)}) ครบกำหนดไถ่ถอน/คืนเงินแล้ว ณ วันที่ ${formatDate(contract.endDate)}`;
                    alertClass = 'bg-blue-50 border-blue-400 text-blue-700';
                    iconClass = 'fas fa-info-circle text-blue-500';
                    hasAlerts = true;
                }

                if (alertMessage) {
                    const alertDiv = document.createElement('div');
                    alertDiv.className = `p-4 rounded-md shadow-sm flex items-start space-x-3 ${alertClass}`;
                    alertDiv.innerHTML = `
                        <i class="${iconClass} text-xl mt-1"></i>
                        <div>
                            <p class="font-bold">${alertMessage}</p>
                            <p class="text-sm">ประเภท: ${contract.type} | วันที่ทำสัญญา: ${formatDate(contract.startDate)}</p>
                        </div>
                    `;
                    alertList.appendChild(alertDiv);
                }
            });

            if (!hasAlerts) {
                alertList.innerHTML = `
                    <div class="bg-green-50 border-l-4 border-green-400 text-green-700 p-4 rounded-md shadow-sm" role="alert">
                        <p class="font-bold">ไม่มีการแจ้งเตือนในขณะนี้</p>
                        <p class="text-sm">ข้อมูลล่าสุด ณ ปัจจุบัน</p>
                    </div>
                `;
            }
        }

        /**
         * Renders the list of contracts in the table.
         */
        function renderContractsList() {
            updateContractStatuses(); // Ensure statuses are up-to-date
            const searchTerm = searchContract.value.toLowerCase();
            const filterStatusValue = filterStatus.value;
            const filterTypeValue = filterType.value;

            const filteredContracts = contracts.filter(contract => {
                const matchesSearch = (
                    contract.borrowerName.toLowerCase().includes(searchTerm) ||
                    (contract.propertyDetails && contract.propertyDetails.landDeedNo && contract.propertyDetails.landDeedNo.toLowerCase().includes(searchTerm)) ||
                    contract.phoneNumber.includes(searchTerm) ||
                    (contract.brokerName && contract.brokerName.toLowerCase().includes(searchTerm))
                );
                const matchesStatus = filterStatusValue === '' || contract.status === filterStatusValue;
                const matchesType = filterTypeValue === '' || contract.type === filterTypeValue;
                return matchesSearch && matchesStatus && matchesType;
            });

            contractsTableBody.innerHTML = '';
            if (filteredContracts.length === 0) {
                contractsTableBody.innerHTML = `<tr><td colspan="6" class="text-center py-4 text-gray-500">ไม่พบข้อมูลสัญญา</td></tr>`;
                return;
            }

            filteredContracts.forEach(contract => {
                const row = document.createElement('tr');
                row.className = 'border-b border-gray-200 hover:bg-gray-50 transition-colors';

                // Display the manually entered next payment due date
                const displayNextDueDate = contract.nextPaymentDueDateManual ? formatDate(contract.nextPaymentDueDateManual) : 'ไม่มี';
                const statusClass = `status-${contract.status.toLowerCase().replace(/\s/g, '-')}`;

                row.innerHTML = `
                    <td class="py-3 px-4 text-gray-800 font-medium">${contract.borrowerName}</td>
                    <td class="py-3 px-4 text-gray-700">${contract.type}</td>
                    <td class="py-3 px-4 text-gray-700">${formatCurrency(contract.principalAmount)}</td>
                    <td class="py-3 px-4 text-gray-700">${displayNextDueDate}</td>
                    <td class="py-3 px-4">
                        <span class="px-3 py-1 rounded-full text-xs font-semibold ${statusClass}">
                            ${(() => {
                                switch (contract.status) {
                                    case 'Active': return 'ใช้งานอยู่';
                                    case 'Nearing Due': return 'ใกล้ครบกำหนด';
                                    case 'Overdue': return 'เกินกำหนดชำระ';
                                    case 'Matured': return 'ครบกำหนดไถ่ถอน/คืนเงิน';
                                    case 'Redeemed': return 'ไถ่ถอนแล้ว/ชำระคืนแล้ว';
                                    default: return contract.status;
                                }
                            })()}
                        </span>
                    </td>
                    <td class="py-3 px-4 space-x-2 flex flex-wrap gap-2">
                        <button data-id="${contract.id}" class="view-payments-btn bg-blue-500 hover:bg-blue-600 text-white p-2 rounded-md text-sm shadow-sm transition-colors" title="ดูประวัติการชำระ">
                            <i class="fas fa-history"></i>
                        </button>
                        <button data-id="${contract.id}" class="edit-contract-btn bg-yellow-500 hover:bg-yellow-600 text-white p-2 rounded-md text-sm shadow-sm transition-colors" title="แก้ไขสัญญา">
                            <i class="fas fa-edit"></i>
                        </button>
                        <button data-id="${contract.id}" class="delete-contract-btn bg-red-500 hover:bg-red-600 text-white p-2 rounded-md text-sm shadow-sm transition-colors" title="ลบสัญญา">
                            <i class="fas fa-trash-alt"></i>
                        </button>
                        ${contract.status !== 'Redeemed' ? `
                        <button data-id="${contract.id}" class="redeem-contract-btn bg-gray-500 hover:bg-gray-600 text-white p-2 rounded-md text-sm shadow-sm transition-colors" title="เปลี่ยนสถานะเป็นไถ่ถอนแล้ว">
                            <i class="fas fa-check"></i>
                        </button>` : ''}
                    </td>
                `;
                contractsTableBody.appendChild(row);
            });

            // Attach event listeners to new buttons
            document.querySelectorAll('.view-payments-btn').forEach(button => {
                button.onclick = (e) => showPaymentHistoryModal(e.currentTarget.dataset.id);
            });
            document.querySelectorAll('.edit-contract-btn').forEach(button => {
                button.onclick = (e) => showAddEditModal(e.currentTarget.dataset.id);
            });
            document.querySelectorAll('.delete-contract-btn').forEach(button => {
                button.onclick = (e) => confirmDeleteContract(e.currentTarget.dataset.id);
            });
            document.querySelectorAll('.redeem-contract-btn').forEach(button => {
                button.onclick = (e) => confirmRedeemContract(e.currentTarget.dataset.id);
            });
        }

        /**
         * Renders the payment history for a specific contract.
         * @param {string} contractId - The ID of the contract.
         */
        function renderPaymentHistory(contractId) {
            const contract = contracts.find(c => c.id === contractId);
            if (!contract) return;

            paymentHistoryTableBody.innerHTML = '';
            if (!contract.payments || contract.payments.length === 0) {
                paymentHistoryTableBody.innerHTML = `<tr><td colspan="3" class="text-center py-4 text-gray-500">ยังไม่มีประวัติการชำระ</td></tr>`;
                return;
            }

            // Sort payments by date descending
            const sortedPayments = [...contract.payments].sort((a, b) => new Date(b.date) - new Date(a.date));

            sortedPayments.forEach(payment => {
                const row = document.createElement('tr');
                row.className = 'border-b border-gray-200 hover:bg-gray-50 transition-colors';
                row.innerHTML = `
                    <td class="py-3 px-4 text-gray-800">${formatDate(payment.date)}</td>
                    <td class="py-3 px-4 text-gray-800">${formatCurrency(payment.amount)}</td>
                    <td class="py-3 px-4">
                        <button data-contract-id="${contractId}" data-payment-date="${payment.date}" class="delete-payment-btn bg-red-500 hover:bg-red-600 text-white p-2 rounded-md text-sm shadow-sm transition-colors" title="ลบการชำระนี้">
                            <i class="fas fa-times"></i>
                        </button>
                    </td>
                `;
                paymentHistoryTableBody.appendChild(row);
            });

            document.querySelectorAll('.delete-payment-btn').forEach(button => {
                button.onclick = (e) => confirmDeletePayment(e.currentTarget.dataset.contractId, e.currentTarget.dataset.paymentDate);
            });
        }


        // --- Modal Management ---

        /**
         * Shows/hides sections based on navigation.
         * @param {string} sectionId - The ID of the section to show ('dashboard' or 'contracts').
         */
        function showSection(sectionId) {
            dashboardSection.classList.add('hidden');
            contractsSection.classList.add('hidden');

            if (sectionId === 'dashboard') {
                dashboardSection.classList.remove('hidden');
                renderDashboard();
            } else if (sectionId === 'contracts') {
                contractsSection.classList.remove('hidden');
                renderContractsList();
            }
        }

        /**
         * Calculates and updates the displayed actual principal.
         */
        function updateActualPrincipalDisplay() {
            const principal = parseFloat(principalAmountInput.value) || 0;
            const transferFee = parseFloat(transferFeeInput.value) || 0;
            const landDeptServiceFee = parseFloat(landDeptServiceFeeInput.value) || 0;
            const brokerageFee = parseFloat(brokerageFeeInput.value) || 0;
            const prepaidMonths = parseInt(prepaidInterestMonthsInput.value) || 0;
            const interestRate = parseFloat(interestRateInput.value) || 0;

            const monthlyInterest = (principal * interestRate) / 100;
            const prepaidInterestAmount = monthlyInterest * prepaidMonths;

            const actualPrincipal = principal - transferFee - landDeptServiceFee - brokerageFee - prepaidInterestAmount;
            actualPrincipalDisplay.textContent = formatCurrency(actualPrincipal);
        }

        /**
         * Updates the visibility of the property details section based on contract type and hasProperty selection.
         */
        function updatePropertyDetailsVisibility() {
            const isSellMortgage = contractTypeInput.value === 'ขายฝาก';
            const hasPropertySelected = hasPropertyYes.checked;

            if (isSellMortgage && hasPropertySelected) {
                propertyDetailsSection.classList.remove('hidden');
            } else {
                propertyDetailsSection.classList.add('hidden');
                // Clear property details fields if hidden
                propertyTypeInput.value = '';
                landDeedNoInput.value = '';
                propertySizeInput.value = '';
                propertyLocationInput.value = '';
                transferFeeInput.value = '0';
                landDeptServiceFeeInput.value = '0';
                brokerageFeeInput.value = '0';
                prepaidInterestMonthsInput.value = '0'; // Clear new field
            }
            // Always update actual principal display when visibility changes
            updateActualPrincipalDisplay();
        }

        /**
         * Shows the add/edit contract modal.
         * @param {string|null} contractId - The ID of the contract to edit, or null for a new contract.
         */
        function showAddEditModal(contractId = null) {
            contractForm.reset(); // Clear form fields
            contractIdInput.value = ''; // Clear hidden ID
            hasPropertyNo.checked = true; // Default to 'ไม่มีทรัพย์สิน'
            brokerNameInput.value = ''; // Clear broker name
            prepaidInterestMonthsInput.value = '0'; // Default new field
            nextPaymentDueDateManualInput.value = ''; // Clear new manual date field
            
            // Set default dates for new contract (today and 1 year from now)
            const today = new Date();
            const nextYear = new Date();
            nextYear.setFullYear(today.getFullYear() + 1);
            startDateInput.value = today.toISOString().split('T')[0];
            endDateInput.value = nextYear.toISOString().split('T')[0];

            if (contractId) {
                modalTitle.textContent = 'แก้ไขสัญญา';
                const contract = contracts.find(c => c.id === contractId);
                if (contract) {
                    contractIdInput.value = contract.id;
                    borrowerNameInput.value = contract.borrowerName;
                    phoneNumberInput.value = contract.phoneNumber;
                    idCardInput.value = contract.idCard;
                    addressInput.value = contract.address;
                    brokerNameInput.value = contract.brokerName || '';
                    
                    if (contract.hasProperty === true) {
                        hasPropertyYes.checked = true;
                    } else {
                        hasPropertyNo.checked = true;
                    }

                    contractTypeInput.value = contract.type;
                    principalAmountInput.value = contract.principalAmount;
                    interestRateInput.value = contract.interestRate;
                    startDateInput.value = contract.startDate;
                    endDateInput.value = contract.endDate;
                    paymentFrequencyInput.value = contract.paymentFrequency;
                    nextPaymentDueDateManualInput.value = contract.nextPaymentDueDateManual || ''; // Populate manual date

                    // Update visibility and fill property details if applicable
                    if (contract.type === 'ขายฝาก' && contract.hasProperty && contract.propertyDetails) {
                        propertyTypeInput.value = contract.propertyDetails.type || '';
                        landDeedNoInput.value = contract.propertyDetails.landDeedNo || '';
                        propertySizeInput.value = contract.propertyDetails.size || '';
                        propertyLocationInput.value = contract.propertyDetails.location || '';
                        transferFeeInput.value = contract.propertyDetails.transferFee || '0';
                        landDeptServiceFeeInput.value = contract.propertyDetails.landDeptServiceFee || '0';
                        brokerageFeeInput.value = contract.propertyDetails.brokerageFee || '0';
                        prepaidInterestMonthsInput.value = contract.propertyDetails.prepaidInterestMonths || '0'; // Populate new field
                    }
                }
            } else {
                modalTitle.textContent = 'เพิ่มสัญญาใหม่';
            }
            updatePropertyDetailsVisibility(); // Call after populating all fields
            contractModal.classList.remove('hidden');
        }

        /**
         * Hides the add/edit contract modal.
         */
        function hideAddEditModal() {
            contractModal.classList.add('hidden');
        }

        /**
         * Shows the payment history modal for a given contract.
         * @param {string} contractId - The ID of the contract.
         */
        function showPaymentHistoryModal(contractId) {
            currentContractIdForPayment = contractId;
            const contract = contracts.find(c => c.id === contractId);
            if (!contract) {
                showMessageBox('ไม่พบข้อมูลสัญญา', 'error');
                return;
            }

            paymentModalContractName.textContent = contract.borrowerName;
            monthlyInterestAmountDisplay.textContent = formatCurrency(calculateMonthlyInterest(contract));
            paymentDateInput.value = new Date().toISOString().split('T')[0]; // Set default payment date to today
            paymentAmountInput.value = calculateMonthlyInterest(contract); // Pre-fill with calculated monthly interest

            renderPaymentHistory(contractId);
            paymentHistoryModal.classList.remove('hidden');
        }

        /**
         * Hides the payment history modal.
         */
        function hidePaymentHistoryModal() {
            paymentHistoryModal.classList.add('hidden');
            currentContractIdForPayment = null;
        }

        /**
         * Shows the confirmation modal.
         * @param {string} title - Title for the confirmation modal.
         * @param {string} message - Message for the confirmation modal.
         * @param {function} onConfirm - Callback function to execute on confirmation.
         */
        function showConfirmationModal(title, message, onConfirm) {
            confirmModalTitle.textContent = title;
            confirmModalMessage.textContent = message;
            confirmActionBtn.onclick = () => {
                onConfirm();
                hideConfirmationModal();
            };
            confirmationModal.classList.remove('hidden');
        }

        /**
         * Hides the confirmation modal.
         */
        function hideConfirmationModal() {
            confirmationModal.classList.add('hidden');
        }

        // --- Event Handlers ---

        /**
         * Handles form submission for adding or updating a contract.
         * @param {Event} e - The form submit event.
         */
        function handleContractFormSubmit(e) {
            e.preventDefault();

            const isEditing = !!contractIdInput.value;
            const contractId = isEditing ? contractIdInput.value : generateUUID();

            const principal = parseFloat(principalAmountInput.value);
            const interest = parseFloat(interestRateInput.value);
            const hasProperty = hasPropertyYes.checked;

            // Input validation for phone number and ID card
            const phoneNumber = phoneNumberInput.value.trim();
            const idCard = idCardInput.value.trim();

            if (phoneNumber.length > 0 && (phoneNumber.length !== 10 || !/^[0-9]{10}$/.test(phoneNumber))) {
                showMessageBox('กรุณากรอกเบอร์โทรศัพท์ให้ถูกต้อง (10 หลักตัวเลข)', 'error');
                return;
            }
            if (idCard.length > 0 && (idCard.length !== 13 || !/^[0-9]{13}$/.test(idCard))) {
                showMessageBox('กรุณากรอกเลขบัตรประชาชนให้ถูกต้อง (13 หลักตัวเลข)', 'error');
                return;
            }

            if (isNaN(principal) || principal <= 0) {
                showMessageBox('กรุณากรอกเงินต้นให้ถูกต้อง', 'error');
                return;
            }
            if (isNaN(interest) || interest < 0) {
                showMessageBox('กรุณากรอกอัตราดอกเบี้ยให้ถูกต้อง', 'error');
                return;
            }
            if (new Date(startDateInput.value) > new Date(endDateInput.value)) {
                showMessageBox('วันที่ครบกำหนดต้องไม่ก่อนวันที่ทำสัญญา', 'error');
                return;
            }
            if (nextPaymentDueDateManualInput.value && new Date(nextPaymentDueDateManualInput.value) < new Date(startDateInput.value)) {
                showMessageBox('กำหนดชำระถัดไปต้องไม่ก่อนวันที่ทำสัญญา', 'error');
                return;
            }


            const newContract = {
                id: contractId,
                borrowerName: borrowerNameInput.value.trim(),
                phoneNumber: phoneNumber, // Use validated phone number
                idCard: idCard,         // Use validated ID card
                address: addressInput.value.trim(),
                brokerName: brokerNameInput.value.trim(),
                hasProperty: hasProperty,
                type: contractTypeInput.value,
                principalAmount: principal,
                interestRate: interest,
                startDate: startDateInput.value,
                endDate: endDateInput.value,
                paymentFrequency: paymentFrequencyInput.value,
                nextPaymentDueDateManual: nextPaymentDueDateManualInput.value, // Store manual date
                status: 'Active', // Default status, will be updated by updateContractStatuses
                propertyDetails: null,
                payments: []
            };

            if (newContract.type === 'ขายฝาก' && newContract.hasProperty) {
                const transferFee = parseFloat(transferFeeInput.value) || 0;
                const landDeptServiceFee = parseFloat(landDeptServiceFeeInput.value) || 0;
                const brokerageFee = parseFloat(brokerageFeeInput.value) || 0;
                const prepaidMonths = parseInt(prepaidInterestMonthsInput.value) || 0;
                const monthlyInterest = (principal * interest) / 100;
                const prepaidInterestAmount = monthlyInterest * prepaidMonths;
                const actualPrincipal = principal - transferFee - landDeptServiceFee - brokerageFee - prepaidInterestAmount;

                newContract.propertyDetails = {
                    type: propertyTypeInput.value.trim(),
                    landDeedNo: landDeedNoInput.value.trim(),
                    size: propertySizeInput.value.trim(),
                    location: propertyLocationInput.value.trim(),
                    transferFee: transferFee,
                    landDeptServiceFee: landDeptServiceFee,
                    brokerageFee: brokerageFee,
                    prepaidInterestMonths: prepaidMonths,
                    prepaidInterestAmount: prepaidInterestAmount,
                    actualPrincipal: actualPrincipal
                };
            }

            if (isEditing) {
                const index = contracts.findIndex(c => c.id === contractId);
                if (index !== -1) {
                    newContract.payments = contracts[index].payments || [];
                    contracts[index] = newContract;
                    showMessageBox('แก้ไขสัญญาสำเร็จ!', 'success');
                }
            } else {
                contracts.push(newContract);
                showMessageBox('เพิ่มสัญญาใหม่สำเร็จ!', 'success');
            }

            saveContracts();
            hideAddEditModal();
            renderContractsList(); // This will call updateContractStatuses
            renderDashboard();    // This will call updateContractStatuses
        }

        /**
         * Confirms and deletes a contract.
         * @param {string} id - The ID of the contract to delete.
         */
        function confirmDeleteContract(id) {
            const contract = contracts.find(c => c.id === id);
            if (!contract) return;
            showConfirmationModal(
                'ยืนยันการลบสัญญา',
                `คุณแน่ใจหรือไม่ที่ต้องการลบสัญญาของ ${contract.borrowerName} (เงินต้น ${formatCurrency(contract.principalAmount)})? การดำเนินการนี้ไม่สามารถย้อนกลับได้`,
                () => {
                    contracts = contracts.filter(c => c.id !== id);
                    saveContracts();
                    renderContractsList();
                    renderDashboard();
                    showMessageBox('ลบสัญญาสำเร็จ!', 'success');
                }
            );
        }

        /**
         * Confirms and marks a contract as redeemed.
         * @param {string} id - The ID of the contract to redeem.
         */
        function confirmRedeemContract(id) {
            const contract = contracts.find(c => c.id === id);
            if (!contract) return;
            showConfirmationModal(
                'ยืนยันการไถ่ถอน/ชำระคืน',
                `คุณแน่ใจหรือไม่ที่ต้องการเปลี่ยนสถานะสัญญาของ ${contract.borrowerName} เป็น "ไถ่ถอนแล้ว/ชำระคืนแล้ว"?`,
                () => {
                    const index = contracts.findIndex(c => c.id === id);
                    if (index !== -1) {
                        contracts[index].status = 'Redeemed';
                        saveContracts();
                        renderContractsList();
                        renderDashboard();
                        showMessageBox('เปลี่ยนสถานะสัญญาเป็น "ไถ่ถอนแล้ว" สำเร็จ!', 'success');
                    }
                }
            );
        }

        /**
         * Handles adding a new payment to the current contract.
         */
        function handleAddPayment() {
            if (!currentContractIdForPayment) return;

            const contract = contracts.find(c => c.id === currentContractIdForPayment);
            if (!contract) {
                showMessageBox('ไม่พบข้อมูลสัญญา', 'error');
                return;
            }

            const paymentDate = paymentDateInput.value;
            const paymentAmount = parseFloat(paymentAmountInput.value);

            if (!paymentDate || isNaN(paymentAmount) || paymentAmount <= 0) {
                showMessageBox('กรุณากรอกวันที่และจำนวนเงินที่ชำระให้ถูกต้อง', 'error');
                return;
            }

            if (!contract.payments) {
                contract.payments = [];
            }
            contract.payments.push({
                date: paymentDate,
                amount: paymentAmount
            });

            saveContracts();
            renderPaymentHistory(currentContractIdForPayment);
            renderContractsList();
            renderDashboard();
            showMessageBox('บันทึกการชำระสำเร็จ!', 'success');
        }

        /**
         * Confirms and deletes a specific payment from a contract.
         * @param {string} contractId - The ID of the contract.
         * @param {string} paymentDate - The date of the payment to delete.
         */
        function confirmDeletePayment(contractId, paymentDate) {
            const contract = contracts.find(c => c.id === contractId);
            if (!contract) return;

            showConfirmationModal(
                'ยืนยันการลบการชำระ',
                `คุณแน่ใจหรือไม่ที่ต้องการลบการชำระดอกเบี้ยในวันที่ ${formatDate(paymentDate)} ของ ${contract.borrowerName}?`,
                () => {
                    contract.payments = contract.payments.filter(p => p.date !== paymentDate);
                    saveContracts();
                    renderPaymentHistory(contractId);
                    renderContractsList();
                    renderDashboard();
                    showMessageBox('ลบการชำระสำเร็จ!', 'success');
                }
            );
        }


        // --- Event Listeners ---

        navDashboard.addEventListener('click', () => showSection('dashboard'));
        navContracts.addEventListener('click', () => showSection('contracts'));
        addContractBtn.addEventListener('click', () => showAddEditModal());
        cancelContractModalBtn.addEventListener('click', hideAddEditModal);
        contractForm.addEventListener('submit', handleContractFormSubmit);
        closePaymentHistoryModalBtn.addEventListener('click', hidePaymentHistoryModal);
        addPaymentBtn.addEventListener('click', handleAddPayment);
        cancelConfirmBtn.addEventListener('click', hideConfirmationModal);

        // Event listeners for property details visibility and actual principal calculation
        hasPropertyYes.addEventListener('change', updatePropertyDetailsVisibility);
        hasPropertyNo.addEventListener('change', updatePropertyDetailsVisibility);
        contractTypeInput.addEventListener('change', updatePropertyDetailsVisibility);

        // Event listeners for real-time actual principal calculation
        principalAmountInput.addEventListener('input', updateActualPrincipalDisplay);
        interestRateInput.addEventListener('input', updateActualPrincipalDisplay);
        transferFeeInput.addEventListener('input', updateActualPrincipalDisplay);
        landDeptServiceFeeInput.addEventListener('input', updateActualPrincipalDisplay);
        brokerageFeeInput.addEventListener('input', updateActualPrincipalDisplay);
        prepaidInterestMonthsInput.addEventListener('change', updateActualPrincipalDisplay);


        // Search and Filter Event Listeners
        searchContract.addEventListener('input', renderContractsList);
        filterStatus.addEventListener('change', renderContractsList);
        filterType.addEventListener('change', renderContractsList);

        // Initial App Load
        document.addEventListener('DOMContentLoaded', () => {
            loadContracts();
            showSection('dashboard'); // Start on the dashboard
        });
    </script>
</body>
</html>
