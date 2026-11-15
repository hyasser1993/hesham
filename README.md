# hesham
el yousr printing 
<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>شركة اليسر للطباعة - إدارة عروض الأسعار والمندوبين</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Cairo:wght@400;600;700&display=swap" rel="stylesheet">
    <style>
        body { font-family: 'Cairo', sans-serif; background-color: #f3f4f6; }
        @media print {
            body * { visibility: hidden; }
            #printableArea, #printableArea * { visibility: visible; }
            #printableArea { position: absolute; left: 0; top: 0; width: 100%; background: white; padding: 20px; }
            .no-print { display: none !important; }
        }
    </style>
</head>
<body class="p-4 md:p-6">

    <!-- شاشة اختيار الصلاحية / تسجيل الدخول -->
    <div id="authScreen" class="max-w-md mx-auto bg-white rounded-2xl shadow-xl p-8 mt-20 text-center">
        <h2 class="text-2xl font-bold text-blue-900 mb-2">شركة اليسر للطباعة</h2>
        <p class="text-gray-600 text-sm mb-6">النظام السحابي الموحد (Firebase)</p>
        <div class="space-y-4">
            <button onclick="loginAs('admin')" class="w-full bg-blue-900 text-white py-3 rounded-xl font-bold shadow hover:bg-blue-800 transition">الدخول بصفتك (مدير - Admin)</button>
            <button onclick="loginAs('employee')" class="w-full bg-indigo-600 text-white py-3 rounded-xl font-bold shadow hover:bg-indigo-700 transition">الدخول بصفتك (موظف / مندوب)</button>
        </div>
    </div>

    <!-- التطبيق الرئيسي -->
    <div id="mainApp" class="max-w-7xl mx-auto bg-white rounded-2xl shadow-xl overflow-hidden hidden">
        <!-- ترويسة التطبيق -->
        <div class="bg-gradient-to-r from-blue-900 to-indigo-800 text-white p-6 flex flex-col md:flex-row justify-between items-center gap-4 no-print">
            <div>
                <h1 class="text-2xl font-bold">شركة اليسر للطباعة</h1>
                <p class="text-blue-200 text-sm mt-1">نظام سحابي متصل بـ Firebase (<span id="userRoleBadge" class="font-bold underline"></span>)</p>
            </div>
            <div class="flex gap-2 items-center">
                <button onclick="switchTab('quotations')" id="btn-quotations" class="bg-white text-blue-900 px-4 py-2 rounded-lg font-bold shadow transition">عروض الأسعار والمقايسات</button>
                <button onclick="switchTab('reps')" id="btn-reps" class="bg-blue-800 text-white px-4 py-2 rounded-lg font-bold shadow hover:bg-blue-700 transition">متابعة المندوبين والزيارات</button>
                <button onclick="logout()" class="bg-red-600 hover:bg-red-700 text-white px-3 py-2 rounded-lg text-sm font-bold shadow mr-4">خروج</button>
            </div>
        </div>

        <!-- محتوى قسم عروض الأسعار -->
        <div id="tab-quotations" class="p-6">
            <div class="flex justify-between items-center mb-6 no-print">
                <h2 class="text-xl font-bold text-gray-800">إدارة عروض الأسعار والمقايسات (سحابي)</h2>
                <button onclick="openNewQuotationModal()" class="bg-green-600 hover:bg-green-700 text-white px-4 py-2 rounded-lg font-bold shadow">+ إنشاء عرض سعر جديد</button>
            </div>

            <!-- جدول عروض الأسعار السحابية -->
            <div class="overflow-x-auto no-print">
                <table class="w-full border-collapse border border-gray-200 text-right">
                    <thead>
                        <tr class="bg-gray-100 text-gray-700">
                            <th class="border p-3">رقم العرض</th>
                            <th class="border p-3">اسم العميل</th>
                            <th class="border p-3">التاريخ</th>
                            <th class="border p-3">الإجمالي النهائي</th>
                            <th class="border p-3 text-center">الإجراءات</th>
                        </tr>
                    </thead>
                    <tbody id="quotationsTableBody">
                        <!-- تظهر البيانات من Firebase هنا -->
                    </tbody>
                </table>
            </div>

            <!-- منطقة معاينة وطباعة عرض السعر A4 -->
            <div id="printableArea" class="hidden bg-white p-8 max-w-4xl mx-auto border rounded-xl">
                <div class="flex justify-between items-border border-b pb-6 mb-6">
                    <div>
                        <h2 class="text-2xl font-bold text-blue-900">شركة اليسر للطباعة</h2>
                        <p class="text-sm text-gray-600 mt-1">عروض أسعار رسمية ومعتمدة للمقايسات</p>
                    </div>
                    <div class="text-left">
                        <h3 class="text-xl font-bold text-gray-800">عرض سعر</h3>
                        <p class="text-sm text-gray-600">رقم العرض: <span id="pQNum" class="font-bold"></span></p>
                        <p class="text-sm text-gray-600">التاريخ: <span id="pDate"></span></p>
                    </div>
                </div>

                <div class="mb-6 bg-gray-50 p-4 rounded-lg">
                    <p class="text-sm text-gray-600">موجّه إلى السيد / الجهة:</p>
                    <h4 class="text-lg font-bold text-gray-900" id="pCustomer"></h4>
                </div>

                <div class="mb-6">
                    <table class="w-full border-collapse border border-gray-300 text-right text-sm">
                        <thead>
                            <tr class="bg-gray-100 text-gray-800">
                                <th class="border p-2 w-10 text-center">م</th>
                                <th class="border p-2">رقم المقايسة</th>
                                <th class="border p-2">اسم البند والخدمة</th>
                                <th class="border p-2">المواصفة الفنية</th>
                                <th class="border p-2">المقاس / الأبعاد</th>
                                <th class="border p-2">الوحدة</th>
                                <th class="border p-2 text-center">الكمية</th>
                                <th class="border p-2 text-left">سعر الوحدة</th>
                                <th class="border p-2 text-left">الإجمالي</th>
                            </tr>
                        </thead>
                        <tbody id="pItemsBody">
                            <!-- البنود للطباعة -->
                        </tbody>
                    </table>
                </div>

                <div class="flex justify-between items-start border-t pt-4">
                    <div>
                        <p class="text-sm text-gray-600 font-semibold">المبلغ بالحروف:</p>
                        <p id="pAmountInWords" class="text-sm font-bold text-blue-900 mt-1">-</p>
                    </div>
                    <div class="text-left">
                        <p class="text-xl font-bold text-gray-900">الإجمالي النهائي: <span id="pGrandTotal" class="text-green-600">0.00</span> ج.م</p>
                    </div>
                </div>

                <div class="mt-12 pt-8 border-t flex justify-between text-sm text-gray-600">
                    <div>توقيع المسؤول: ..........................</div>
                    <div>ختم الشركة: ..........................</div>
                </div>
            </div>
            
            <!-- أزرار التحكم في وضع المعاينة -->
            <div id="printActionsBar" class="hidden flex justify-end gap-3 mt-6 no-print">
                <button onclick="closePrintPreview()" class="bg-gray-500 text-white px-5 py-2 rounded-lg font-bold">العودة للقائمة</button>
                <button onclick="window.print()" class="bg-blue-600 hover:bg-blue-700 text-white px-6 py-2 rounded-lg font-bold shadow">طباعة المستند / حفظ PDF</button>
            </div>
        </div>

        <!-- محتوى قسم المندوبين والزيارات -->
        <div id="tab-reps" class="p-6 hidden no-print">
            <div class="flex justify-between items-center mb-6">
                <h2 class="text-xl font-bold text-gray-800">سجل زيارات مندوبي المبيعات (سحابي)</h2>
                <button onclick="openNewVisitModal()" class="bg-indigo-600 hover:bg-indigo-700 text-white px-4 py-2 rounded-lg font-bold shadow">+ تسجيل زيارة جديدة</button>
            </div>

            <div class="overflow-x-auto">
                <table class="w-full border-collapse border border-gray-200 text-right">
                    <thead>
                        <tr class="bg-gray-100 text-gray-700">
                            <th class="border p-3">التاريخ</th>
                            <th class="border p-3">اسم المندوب</th>
                            <th class="border p-3">اسم العميل</th>
                            <th class="border p-3">نوع المقابلة</th>
                            <th class="border p-3">حالة الزيارة</th>
                            <th class="border p-3">الملاحظات ورقم المقايسة</th>
                        </tr>
                    </thead>
                    <tbody id="visitsTableBody">
                        <!-- تظهر زيارات المندوبين من Firebase هنا -->
                    </tbody>
                </table>
            </div>
        </div>
    </div>

    <!-- نافذة إنشاء/تعديل عرض سعر -->
    <div id="quotationModal" class="fixed inset-0 bg-black bg-opacity-50 hidden flex items-center justify-center p-4 overflow-y-auto z-50">
        <div class="bg-white rounded-xl max-w-7xl w-full p-6 max-h-[90vh] overflow-y-auto">
            <h3 id="modalTitle" class="text-xl font-bold mb-4 text-gray-800">إنشاء عرض سعر جديد وبنود المقايسة</h3>
            <input type="hidden" id="editingId" value="">
            
            <div class="grid grid-cols-1 md:grid-cols-2 gap-4 mb-4">
                <div>
                    <label class="block text-sm font-semibold mb-1">اسم العميل</label>
                    <input type="text" id="qCustomer" class="w-full border p-2 rounded-lg" placeholder="اسم الشركة أو العميل">
                </div>
                <div>
                    <label class="block text-sm font-semibold mb-1">رقم العرض</label>
                    <input type="text" id="qNumber" class="w-full border p-2 rounded-lg" value="QUO-2026-01">
                </div>
            </div>

            <!-- جدول البنود ورقم المقايسة والمواصفة -->
            <div class="mb-4">
                <div class="flex justify-between items-center mb-2">
                    <h4 class="font-bold text-gray-700">بنود عرض السعر والمقايسات والمواصفات</h4>
                    <button onclick="addItemRow()" class="bg-blue-600 text-white px-3 py-1 rounded text-sm">+ إضافة بند جديد</button>
                </div>
                <div class="overflow-x-auto">
                    <table class="w-full border text-sm" id="itemsTable">
                        <thead>
                            <tr class="bg-gray-50">
                                <th class="border p-2 w-28">رقم المقايسة</th>
                                <th class="border p-2 w-40">اسم البند</th>
                                <th class="border p-2">المواصفة الفنية</th>
                                <th class="border p-2 w-28">المقاس / الأبعاد</th>
                                <th class="border p-2 w-20">الوحدة</th>
                                <th class="border p-2 w-16">الكمية</th>
                                <th class="border p-2 w-24">سعر الوحدة</th>
                                <th class="border p-2 w-24">الإجمالي</th>
                                <th class="border p-2 w-12 text-center">حذف</th>
                            </tr>
                        </thead>
                        <tbody id="itemsBody">
                            <!-- البنود تضاف ديناميكياً -->
                        </tbody>
                    </table>
                </div>
            </div>

            <!-- إجماليات -->
            <div class="bg-gray-50 p-4 rounded-lg mb-4 flex flex-col md:flex-row justify-between items-center gap-4">
                <div>
                    <p class="text-sm text-gray-600">المبلغ بالحروف (التفقيط):</p>
                    <p id="amountInWords" class="font-bold text-blue-900">-</p>
                </div>
                <div class="text-left">
                    <p class="text-lg font-bold">الإجمالي النهائي: <span id="grandTotal" class="text-green-600">0.00</span> ج.م</p>
                </div>
            </div>

            <div class="flex justify-end gap-2">
                <button onclick="closeQuotationModal()" class="bg-gray-400 text-white px-4 py-2 rounded-lg">إلغاء</button>
                <button onclick="saveQuotationToFirebase()" class="bg-green-600 hover:bg-green-700 text-white px-5 py-2 rounded-lg font-bold">حفظ سحابياً</button>
            </div>
        </div>
    </div>

    <!-- نافذة تسجيل زيارة جديدة للمندوبين -->
    <div id="visitModal" class="fixed inset-0 bg-black bg-opacity-50 hidden flex items-center justify-center p-4 overflow-y-auto z-50">
        <div class="bg-white rounded-xl max-w-lg w-full p-6">
            <h3 class="text-xl font-bold mb-4 text-gray-800">تسجيل زيارة ميدانية جديدة</h3>
            
            <div class="space-y-4">
                <div>
                    <label class="block text-sm font-semibold mb-1">اسم المندوب</label>
                    <input type="text" id="vRepName" class="w-full border p-2 rounded-lg" placeholder="اسم مندوب المبيعات">
                </div>
                <div>
                    <label class="block text-sm font-semibold mb-1">اسم العميل</label>
                    <input type="text" id="vCustomer" class="w-full border p-2 rounded-lg" placeholder="اسم الشركة أو العميل">
                </div>
                <div>
                    <label class="block text-sm font-semibold mb-1">نوع المقابلة</label>
                    <select id="vType" class="w-full border p-2 rounded-lg">
                        <option value="متابعة دورية">متابعة دورية</option>
                        <option value="عميل جديد">عميل جديد</option>
                        <option value="تحصيل مالي">تحصيل مالي</option>
                        <option value="عرض أسعار / مقايسة">عرض أسعار / مقايسة</option>
                    </select>
                </div>
                <div>
                    <label class="block text-sm font-semibold mb-1">حالة الزيارة</label>
                    <select id="vStatus" class="w-full border p-2 rounded-lg">
                        <option value="تمت بنجاح">تمت بنجاح</option>
                        <option value="مؤجلة">مؤجلة</option>
                        <option value="لم يتم الرد">لم يتم الرد</option>
                    </select>
                </div>
                <div>
                    <label class="block text-sm font-semibold mb-1">الملاحظات ورقم المقايسة (BOQ)</label>
                    <textarea id="vNotes" class="w-full border p-2 rounded-lg" placeholder="تفاصيل المقابلة أو رقم المقايسة المرتبطة..."></textarea>
                </div>
            </div>

            <div class="flex justify-end gap-2 mt-6">
                <button onclick="closeVisitModal()" class="bg-gray-400 text-white px-4 py-2 rounded-lg">إلغاء</button>
                <button onclick="saveVisitToFirebase()" class="bg-indigo-600 hover:bg-indigo-700 text-white px-5 py-2 rounded-lg font-bold">حفظ الزيارة سحابياً</button>
            </div>
        </div>
    </div>

    <!-- كود ربط Firebase والمنطق البرمجي -->
    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.8.0/firebase-app.js";
        import { getFirestore, collection, addDoc, getDocs, doc, updateDoc, deleteDoc } from "https://www.gstatic.com/firebasejs/10.8.0/firebase-firestore.js";

        const firebaseConfig = {
            apiKey: "AIzaSyD98akcEnwZI00SPfA8gWY79MHhQUm0qLo",
            authDomain: "el-yousr-printing.firebaseapp.com",
            projectId: "el-yousr-printing",
            storageBucket: "el-yousr-printing.firebasestorage.app",
            messagingSenderId: "575236759398",
            appId: "1:575236759398:web:1fef8d489a1407c628f2a7",
            measurementId: "G-MYEJR64KT0"
        };

        const app = initializeApp(firebaseConfig);
        const db = getFirestore(app);

        let currentUserRole = localStorage.getItem('current_user_role') || '';

        window.checkAuthState = function() {
            if(currentUserRole) {
                document.getElementById('authScreen').classList.add('hidden');
                document.getElementById('mainApp').classList.remove('hidden');
                document.getElementById('userRoleBadge').innerText = (currentUserRole === 'admin') ? 'مدير' : 'موظف / مندوب';
                loadQuotations();
            } else {
                document.getElementById('authScreen').classList.remove('hidden');
                document.getElementById('mainApp').classList.add('hidden');
            }
        }

        window.loginAs = function(role) {
            currentUserRole = role;
            localStorage.setItem('current_user_role', role);
            checkAuthState();
        }

        window.logout = function() {
            currentUserRole = '';
            localStorage.removeItem('current_user_role');
            checkAuthState();
        }

        window.switchTab = function(tab) {
            if(tab === 'quotations') {
                document.getElementById('tab-quotations').classList.remove('hidden');
                document.getElementById('tab-reps').classList.add('hidden');
                document.getElementById('btn-quotations').className = "bg-white text-blue-900 px-4 py-2 rounded-lg font-bold shadow";
                document.getElementById('btn-reps').className = "bg-blue-800 text-white px-4 py-2 rounded-lg font-bold shadow";
                loadQuotations();
            } else {
                document.getElementById('tab-quotations').classList.add('hidden');
                document.getElementById('tab-reps').classList.remove('hidden');
                document.getElementById('btn-quotations').className = "bg-blue-800 text-white px-4 py-2 rounded-lg font-bold shadow";
                document.getElementById('btn-reps').className = "bg-white text-blue-900 px-4 py-2 rounded-lg font-bold shadow";
                loadVisits();
            }
        }

        window.openNewQuotationModal = function(editData = null, docId = null) {
            document.getElementById('quotationModal').classList.remove('hidden');
            document.getElementById('itemsBody').innerHTML = '';
            
            if(editData) {
                document.getElementById('modalTitle').innerText = "تعديل عرض السعر وبنود المقايسة";
                document.getElementById('editingId').value = docId;
                document.getElementById('qCustomer').value = editData.customer;
                document.getElementById('qNumber').value = editData.qNum;
                
                editData.items.forEach(item => {
                    addItemRow(item);
                });
            } else {
                document.getElementById('modalTitle').innerText = "إنشاء عرض سعر جديد وبنود المقايسة";
                document.getElementById('editingId').value = '';
                document.getElementById('qCustomer').value = '';
                document.getElementById('qNumber').value = 'QUO-' + Math.floor(1000 + Math.random() * 9000);
                addItemRow();
            }
            calculateTotal();
        }

        window.closeQuotationModal = function() {
            document.getElementById('quotationModal').classList.add('hidden');
        }

        window.openNewVisitModal = function() {
            document.getElementById('visitModal').classList.remove('hidden');
        }

        window.closeVisitModal = function() {
            document.getElementById('visitModal').classList.add('hidden');
        }

        window.addItemRow = function(data = null) {
            const tbody = document.getElementById('itemsBody');
            const row = document.createElement('tr');
            row.innerHTML = `
                <td class="border p-2"><input type="text" class="w-full border p-1 rounded itemBoq" value="${data ? (data.boq || '') : ''}" placeholder="BOQ-01"></td>
                <td class="border p-2"><input type="text" class="w-full border p-1 rounded itemName" value="${data ? data.name : ''}" placeholder="اسم البند"></td>
                <td class="border p-2"><input type="text" class="w-full border p-1 rounded itemSpec" value="${data ? (data.spec || '') : ''}" placeholder="مثال: ورق كوشيه 300 جرام..."></td>
                <td class="border p-2"><input type="text" class="w-full border p-1 rounded itemSize" value="${data ? data.size : ''}" placeholder="120×60 سم"></td>
                <td class="border p-2"><input type="text" class="w-full border p-1 rounded itemUnit" value="${data ? data.unit : ''}" placeholder="متر / عدد"></td>
                <td class="border p-2"><input type="number" class="w-full border p-1 rounded itemQty" value="${data ? data.qty : 1}" oninput="calculateTotal()"></td>
                <td class="border p-2"><input type="number" class="w-full border p-1 rounded itemPrice" value="${data ? data.price : 0}" oninput="calculateTotal()"></td>
                <td class="border p-2 text-center itemTotal font-bold">0.00</td>
                <td class="border p-2 text-center"><button onclick="this.closest('tr').remove(); calculateTotal();" class="text-red-600 font-bold text-lg">×</button></td>
            `;
            tbody.appendChild(row);
            calculateTotal();
        }

        window.calculateTotal = function() {
            let grandTotal = 0;
            document.querySelectorAll('#itemsBody tr').forEach(row => {
                const qty = parseFloat(row.querySelector('.itemQty').value) || 0;
                const price = parseFloat(row.querySelector('.itemPrice').value) || 0;
                const total = qty * price;
                row.querySelector('.itemTotal').innerText = total.toFixed(2);
                grandTotal += total;
            });
            document.getElementById('grandTotal').innerText = grandTotal.toFixed(2);
            document.getElementById('amountInWords').innerText = "فقط " + grandTotal.toFixed(2) + " جنيه مصري لا غير";
        }

        window.saveQuotationToFirebase = async function() {
            const customer = document.getElementById('qCustomer').value;
            const qNum = document.getElementById('qNumber').value;
            const grandTotal = document.getElementById('grandTotal').innerText;
            const editingId = document.getElementById('editingId').value;

            if(!customer) {
                alert('يرجى إدخال اسم العميل');
                return;
            }

            const items = [];
            document.querySelectorAll('#itemsBody tr').forEach(row => {
                items.push({
                    boq: row.querySelector('.itemBoq').value,
                    name: row.querySelector('.itemName').value,
                    spec: row.querySelector('.itemSpec').value,
                    size: row.querySelector('.itemSize').value,
                    unit: row.querySelector('.itemUnit').value,
                    qty: row.querySelector('.itemQty').value,
                    price: row.querySelector('.itemPrice').value,
                    total: row.querySelector('.itemTotal').innerText
                });
            });

            try {
                if(editingId) {
                    const docRef = doc(db, "quotations", editingId);
                    await updateDoc(docRef, { customer, qNum, grandTotal, items });
                    alert('تم تعديل وحفظ عرض السعر سحابياً بنجاح!');
                } else {
                    await addDoc(collection(db, "quotations"), {
                        customer, qNum, grandTotal, items, date: new Date().toISOString().split('T')[0]
                    });
                    alert('تم حفظ عرض السعر بنجاح على سحابة Firebase!');
                }
                closeQuotationModal();
                loadQuotations();
            } catch (e) {
                console.error("خطأ في الحفظ السحابي: ", e);
                alert('فشل الحفظ. تأكد من اتصال الإنترنت أو قواعد أمان Firebase.');
            }
        }

        window.loadQuotations = async function() {
            const tbody = document.getElementById('quotationsTableBody');
            tbody.innerHTML = '<tr><td colspan="5" class="text-center p-4 text-gray-500">جاري التحميل من سحابة Firebase...</td></tr>';
            try {
                const querySnapshot = await getDocs(collection(db, "quotations"));
                if(querySnapshot.empty) {
                    tbody.innerHTML = '<tr><td colspan="5" class="text-center p-4 text-gray-500">لا توجد عروض أسعار مسجلة بعد</td></tr>';
                    return;
                }
                tbody.innerHTML = '';
                querySnapshot.forEach((docSnap) => {
                    const data = docSnap.data();
                    const docId = docSnap.id;
                    
                    let actionButtons = `<button onclick='window.previewQuotation(${JSON.stringify(data)})' class="bg-blue-500 text-white px-3 py-1 rounded text-xs font-bold">معاينة وطباعة</button>`;
                    
                    if(currentUserRole === 'admin') {
                        actionButtons += `
                            <button onclick='window.openNewQuotationModal(${JSON.stringify(data)}, "${docId}")' class="bg-yellow-500 text-white px-3 py-1 rounded text-xs font-bold">تعديل</button>
                            <button onclick="window.deleteQuotation('${docId}')" class="bg-red-500 text-white px-3 py-1 rounded text-xs font-bold">حذف</button>
                        `;
                    }

                    tbody.innerHTML += `
                        <tr class="hover:bg-gray-50">
                            <td class="border p-3">${data.qNum || '-'}</td>
                            <td class="border p-3">${data.customer}</td>
                            <td class="border p-3">${data.date}</td>
                            <td class="border p-3 font-bold text-green-600">${data.grandTotal} ج.م</td>
                            <td class="border p-3 text-center flex justify-center gap-2">
                                ${actionButtons}
                            </td>
                        </tr>
                    `;
                });
            } catch (e) {
                console.error(e);
                tbody.innerHTML = '<tr><td colspan="5" class="text-center p-4 text-red-500">خطأ في الاتصال بقاعدة البيانات السحابية</td></tr>';
            }
        }

        window.previewQuotation = function(data) {
            document.getElementById('pQNum').innerText = data.qNum;
            document.getElementById('pDate').innerText = data.date;
            document.getElementById('pCustomer').innerText = data.customer;
            document.getElementById('pGrandTotal').innerText = data.grandTotal;
            document.getElementById('pAmountInWords').innerText = "فقط " + data.grandTotal + " جنيه مصري لا غير";

            const pItemsBody = document.getElementById('pItemsBody');
            pItemsBody.innerHTML = '';
            data.items.forEach((item, index) => {
                pItemsBody.innerHTML += `
                    <tr>
                        <td class="border p-2 text-center">${index + 1}</td>
                        <td class="border p-2 font-semibold text-blue-700">${item.boq || '-'}</td>
                        <td class="border p-2 font-bold">${item.name || '-'}</td>
                        <td class="border p-2 text-gray-700">${item.spec || '-'}</td>
                        <td class="border p-2">${item.size || '-'}</td>
                        <td class="border p-2">${item.unit || '-'}</td>
                        <td class="border p-2 text-center">${item.qty}</td>
                        <td class="border p-2 text-left">${item.price} ج.م</td>
                        <td class="border p-2 text-left font-bold">${item.total} ج.م</td>
                    </tr>
                `;
            });

            document.getElementById('tab-quotations').children[0].classList.add('hidden');
            document.querySelector('#quotationsTableBody').closest('div').classList.add('hidden');
            document.getElementById('printableArea').classList.remove('hidden');
            document.getElementById('printActionsBar').classList.remove('hidden');
        }

        window.closePrintPreview = function() {
            document.getElementById('printableArea').classList.add('hidden');
            document.getElementById('printActionsBar').classList.add('hidden');
            document.getElementById('tab-quotations').children[0].classList.remove('hidden');
            document.querySelector('#quotationsTableBody').closest('div').classList.remove('hidden');
        }

        window.deleteQuotation = async function(docId) {
            if(currentUserRole !== 'admin') {
                alert('عذراً، صلاحيات الحذف مقتصرة على المدير فقط.');
                return;
            }
            if(confirm('هل أنت متأكد من حذف عرض السعر من السحابة؟')) {
                try {
                    await deleteDoc(doc(db, "quotations", docId));
                    alert('تم الحذف بنجاح');
                    loadQuotations();
                } catch(e) {
                    alert('حدث خطأ أثناء الحذف');
                }
            }
        }

        window.saveVisitToFirebase = async function() {
            const repName = document.getElementById('vRepName').value;
            const customer = document.getElementById('vCustomer').value;
            const type = document.getElementById('vType').value;
            const status = document.getElementById('vStatus').value;
            const notes = document.getElementById('vNotes').value;
            const date = new Date().toISOString().split('T')[0];

            if(!repName || !customer) {
                alert('يرجى إدخال اسم المندوب واسم العميل على الأقل');
                return;
            }

            try {
                await addDoc(collection(db, "visits"), {
                    repName, customer, type, status, notes, date
                });
                alert('تم تسجيل وحفظ الزيارة بنجاح على سحابة Firebase!');
                closeVisitModal();
                loadVisits();
            } catch (e) {
                alert('حدث خطأ أثناء الحفظ السحابي للزيارة');
            }
        }

        window.loadVisits = async function() {
            const tbody = document.getElementById('visitsTableBody');
            tbody.innerHTML = '<tr><td colspan="6" class="text-center p-4 text-gray-500">جاري تحميل سجل زيارات المندوبين...</td></tr>';
            try {
                const querySnapshot = await getDocs(collection(db, "visits"));
                if(querySnapshot.empty) {
                    tbody.innerHTML = '<tr><td colspan="6" class="text-center p-4 text-gray-500">لا توجد زيارات مسجلة بعد</td></tr>';
                    return;
                }
                tbody.innerHTML = '';
                querySnapshot.forEach((doc) => {
                    const data = doc.data();
                    tbody.innerHTML += `
                        <tr class="hover:bg-gray-50">
                            <td class="border p-3">${data.date || '-'}</td>
                            <td class="border p-3">${data.repName || '-'}</td>
                            <td class="border p-3">${data.customer}</td>
                            <td class="border p-3">${data.type}</td>
                            <td class="border p-3 font-semibold text-green-600">${data.status}</td>
                            <td class="border p-3">${data.notes}</td>
                        </tr>
                    `;
                });
            } catch (e) {
                tbody.innerHTML = '<tr><td colspan="6" class="text-center p-4 text-red-500">فشل تحميل الزيارات من السحابة</td></tr>';
            }
        }

        // بدء تشغيل التحقق من حالة الدخول
        window.checkAuthState();
    </script>
</body>
</html>
