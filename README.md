<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ระบบประเมินความพึงพอใจ SMILE</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <link href="https://fonts.googleapis.com/css2?family=Sarabun:wght@300;400;500;600;700&display=swap" rel="stylesheet">
    <style>
        * { font-family: "Sarabun", sans-serif; }
        .page { display: none; }
        .page.active { display: block; }
        
        /* ===== หน้าประเมิน - สีชัดเจน ===== */
        .face-container { display: flex; justify-content: space-between; text-align: center; }
        .face-option { 
            font-size: 2.2rem; 
            cursor: pointer; 
            transition: all 0.25s;
            padding: 8px 4px;
            border-radius: 12px;
            min-width: 60px;
        }
        .face-option:hover { transform: scale(1.15); }
        .face-option.selected { 
            transform: scale(1.25); 
            box-shadow: 0 0 0 3px white, 0 0 0 5px currentColor;
        }
        
        /* สีแยกตามระดับ */
        .face-5 { background: #dcfce7; color: #16a34a; } /* เขียว */
        .face-4 { background: #f0fdf4; color: #22c55e; } /* เขียวอ่อน */
        .face-3 { background: #fef9c3; color: #eab308; } /* เหลือง */
        .face-2 { background: #ffedd5; color: #f97316; } /* ส้ม */
        .face-1 { background: #fee2e2; color: #ef4444; } /* แดง */
        
        /* คำอธิบายสี */
        .legend-box { display: flex; justify-content: space-between; flex-wrap: wrap; gap: 8px; padding: 12px; border-radius: 10px; background: #f8fafc; margin-bottom: 16px; }
        .legend-item { display: flex; align-items: center; gap: 6px; font-size: 0.9rem; }
        .legend-dot { width: 14px; height: 14px; border-radius: 50%; display: inline-block; }
        
        .tab-btn.active { border-bottom: 3px solid #3b82f6; color: #2563eb; font-weight: bold; }
        input, select, textarea { border: 2px solid #e5e7eb; border-radius: 8px; padding: 10px 12px; width: 100%; }
        input:focus, select:focus, textarea:focus { outline: none; border-color: #3b82f6; }
        .btn { padding: 10px 20px; border-radius: 8px; font-weight: 600; cursor: pointer; transition: all 0.2s; }
        .btn-primary { background: #2563eb; color: white; }
        .btn-primary:hover { background: #1d4ed8; }
        .btn-success { background: #16a34a; color: white; }
        .btn-danger { background: #dc2626; color: white; }
        .btn-gray { background: #f3f4f6; color: #374151; }
        .card { background: white; border-radius: 12px; box-shadow: 0 2px 12px rgba(0,0,0,0.08); padding: 20px; margin-bottom: 16px; }
    </style>
</head>
<body class="bg-gray-50 min-h-screen">

<!-- แถบนำทาง -->
<nav class="bg-white shadow-md px-6 py-3 mb-6">
    <div class="max-w-7xl mx-auto flex justify-between items-center">
        <div class="font-bold text-xl text-blue-700">😊 SMILE Satisfaction System</div>
        <div id="nav-links" class="hidden md:flex gap-6">
            <button onclick="showPage('survey')" class="text-gray-600 hover:text-blue-600">แบบประเมิน</button>
            <button onclick="showPage('admin-login')" class="text-gray-600 hover:text-blue-600">ผู้ดูแลระบบ</button>
        </div>
        <button id="logout-btn" class="hidden btn btn-gray text-sm py-2" onclick="logout()">ออกจากระบบ</button>
    </div>
</nav>

<div class="max-w-7xl mx-auto px-4">

<!-- ====== หน้าแบบประเมิน ====== -->
<div id="page-survey" class="page active">
    <div class="max-w-3xl mx-auto">
        <h1 class="text-2xl font-bold text-center text-blue-800 mb-2">📋 แบบประเมินความพึงพอใจผู้รับบริการ</h1>
        <p class="text-center text-gray-500 mb-6">โรงพยาบาลมหาวิทยาลัยนเรศวร — ตามอัตลักษณ์ SMILE</p>

        <div class="card">
            <h2 class="font-bold text-lg mb-3">ส่วนที่ 1 ข้อมูลทั่วไป</h2>
            
            <div class="mb-3">
                <label class="block font-medium mb-1">1. ประเภทผู้รับบริการ</label>
                <select id="s-type">
                    <option value="">-- เลือก --</option>
                    <option>มาตรวจครั้งแรก / ผู้ป่วยใหม่</option>
                    <option>มาตรวจตามนัด / ติดตามรักษาต่อเนื่อง</option>
                    <option>มารับบริการส่งเสริมสุขภาพ / ตรวจสุขภาพ</option>
                    <option>มาจากหน่วยอื่น / รับการส่งต่อ</option>
                </select>
            </div>

            <div class="mb-3">
                <label class="block font-medium mb-1">2. คลินิก/หน่วยงานที่มารับบริการ</label>
                <select id="s-clinic"></select>
            </div>

            <div class="mb-3">
                <label class="block font-medium mb-1">3. สิทธิการรักษาพยาบาล</label>
                <select id="s-right">
                    <option value="">-- เลือก --</option>
                    <option>สิทธิหลักประกันสุขภาพแห่งชาติ (บัตรทอง)</option>
                    <option>สิทธิข้าราชการ / ครอบครัวข้าราชการ</option>
                    <option>สิทธิประกันสังคม</option>
                    <option>ชำระค่าบริการเอง</option>
                </select>
            </div>

            <div class="mb-3">
                <label class="block font-medium mb-1">4. ช่วงอายุ</label>
                <select id="s-age">
                    <option value="">-- เลือก --</option>
                    <option>ต่ำกว่า 18 ปี</option>
                    <option>18–59 ปี</option>
                    <option>60 ปีขึ้นไป</option>
                </select>
            </div>
        </div>

        <div class="card">
            <h2 class="font-bold text-lg mb-3">ส่วนที่ 2 ความพึงพอใจ</h2>
            <p class="text-gray-500 mb-3">เลือกหน้าที่ตรงกับความรู้สึกของท่านมากที่สุด</p>
            
            <div class="legend-box">
                <div class="legend-item"><span class="legend-dot bg-green-500"></span> 😊 ดีมาก</div>
                <div class="legend-item"><span class="legend-dot bg-green-400"></span> 🙂 ดี</div>
                <div class="legend-item"><span class="legend-dot bg-yellow-400"></span> 😐 ปานกลาง</div>
                <div class="legend-item"><span class="legend-dot bg-orange-500"></span> 🙁 ควรปรับปรุง</div>
                <div class="legend-item"><span class="legend-dot bg-red-500"></span> 😞 ต้องแก้ไข</div>
            </div>

            <div id="questions-container"></div>
        </div>

        <div class="card">
            <h2 class="font-bold text-lg mb-3">ส่วนที่ 3 ความคิดเห็นเพิ่มเติม</h2>
            <div class="mb-3">
                <label class="block font-medium mb-1">สิ่งที่ประทับใจ:</label>
                <textarea id="s-impress" rows="2"></textarea>
            </div>
            <div class="mb-3">
                <label class="block font-medium mb-1">สิ่งที่ต้องการปรับปรุง:</label>
                <textarea id="s-improve" rows="2"></textarea>
            </div>
            <div class="mb-3">
                <label class="block font-medium mb-1">ข้อเสนอแนะอื่น ๆ:</label>
                <textarea id="s-suggest" rows="2"></textarea>
            </div>
        </div>

        <div class="text-center mt-6">
            <button class="btn btn-primary text-lg px-8 py-3" onclick="submitSurvey()">✅ ส่งแบบประเมิน</button>
        </div>

        <div id="survey-success" class="hidden card text-center mt-6">
            <h3 class="text-xl font-bold text-green-600 mb-2">✅ ขอบคุณสำหรับความคิดเห็น</h3>
            <p class="text-gray-600">ข้อมูลได้รับการบันทึกแล้ว ขอบคุณที่ร่วมพัฒนาคุณภาพบริการ</p>
            <p class="mt-2 text-sm text-gray-400">รหัสอ้างอิง: <span id="ref-code"></span></p>
            <button class="btn btn-gray mt-4" onclick="resetSurvey()">ทำแบบประเมินอีกครั้ง</button>
        </div>
    </div>
</div>

<!-- ====== หน้าเข้าสู่ระบบ - ไม่แสดงรหัสแล้ว ====== -->
<div id="page-admin-login" class="page">
    <div class="max-w-md mx-auto mt-16">
        <div class="card">
            <h2 class="text-xl font-bold text-center mb-6">🔐 เข้าสู่ระบบผู้ดูแล</h2>
            <div class="mb-3">
                <label class="block font-medium mb-1">รหัสผ่าน</label>
                <input type="password" id="admin-pwd" placeholder="ป้อนรหัสผ่าน">
            </div>
            <button class="btn btn-primary w-full mt-2" onclick="adminLogin()">เข้าสู่ระบบ</button>
            <p class="text-center text-sm text-gray-400 mt-3">เฉพาะผู้ที่ได้รับอนุญาตเท่านั้น</p>
        </div>
    </div>
</div>

<!-- ====== หน้าจัดการระบบ ====== -->
<div id="page-admin" class="page">
    <h1 class="text-2xl font-bold mb-6">⚙️ จัดการข้อมูลระบบ</h1>
    <div class="flex flex-wrap gap-2 mb-6 border-b">
        <button class="tab-btn active px-4 py-2" onclick="showAdminTab('clinics')">หน่วยงาน/คลินิก</button>
        <button class="tab-btn px-4 py-2" onclick="showAdminTab('questions')">ข้อคำถาม</button>
        <button class="tab-btn px-4 py-2" onclick="showAdminTab('data')">ข้อมูลทั้งหมด</button>
        <button class="tab-btn px-4 py-2" onclick="showAdminTab('password')">เปลี่ยนรหัสผ่าน</button>
    </div>

    <div id="tab-clinics" class="admin-tab">
        <div class="card">
            <h3 class="font-bold mb-3">เพิ่มคลินิก/หน่วยงาน</h3>
            <div class="flex gap-2 mb-4">
                <input type="text" id="new-clinic" placeholder="ชื่อคลินิก">
                <button class="btn btn-primary whitespace-nowrap" onclick="addClinic()">+ เพิ่ม</button>
            </div>
            <h4 class="font-medium mb-2">รายการทั้งหมด</h4>
            <div id="clinic-list"></div>
        </div>
    </div>

    <div id="tab-questions" class="admin-tab hidden">
        <div class="card">
            <h3 class="font-bold mb-3">เพิ่ม/แก้ไข ข้อคำถามประเมิน</h3>
            <div class="mb-4 p-4 bg-gray-50 rounded-lg">
                <div class="mb-2"><label class="block font-medium mb-1">หมวด</label>
                    <select id="q-cat">
                        <option value="S">S - ยิ้ม-ทักทาย-ให้เกียรติ</option>
                        <option value="M">M - ไตร่ตรอง-เลือกใช้ถ้อยคำ</option>
                        <option value="I">I - ซื่อสัตย์-จริงใจ</option>
                        <option value="L">L - รับฟัง-เข้าใจ</option>
                        <option value="E">E - เท่าเทียม-ทั่วถึง</option>
                    </select>
                </div>
                <div class="mb-2"><label class="block font-medium mb-1">ข้อความคำถาม</label>
                    <input type="text" id="q-text" placeholder="ป้อนข้อความคำถาม">
                </div>
                <button class="btn btn-primary" onclick="addQuestion()">+ เพิ่มข้อคำถาม</button>
            </div>
            <h4 class="font-medium mb-2">รายการข้อคำถามทั้งหมด</h4>
            <div id="question-list"></div>
        </div>
    </div>

    <div id="tab-data" class="admin-tab hidden">
        <div class="card">
            <h3 class="font-bold mb-3">ข้อมูลการประเมินทั้งหมด: <span id="total-count">0</span> ชุด</h3>
            <div class="overflow-x-auto">
                <table class="w-full text-sm">
                    <thead class="bg-gray-100">
                        <tr>
                            <th class="p-2 text-left">วันที่</th>
                            <th class="p-2 text-left">รหัส</th>
                            <th class="p-2 text-left">คลินิก</th>
                            <th class="p-2 text-left">คะแนนเฉลี่ย</th>
                            <th class="p-2 text-left">ระดับ</th>
                        </tr>
                    </thead>
                    <tbody id="all-data-list"></tbody>
                </table>
            </div>
        </div>
    </div>

    <!-- แท็บเปลี่ยนรหัสผ่าน -->
    <div id="tab-password" class="admin-tab hidden">
        <div class="card max-w-md">
            <h3 class="font-bold mb-3">เปลี่ยนรหัสผ่านผู้ดูแล</h3>
            <div class="mb-3">
                <label class="block font-medium mb-1">รหัสผ่านเดิม</label>
                <input type="password" id="current-pwd" placeholder="ป้อนรหัสผ่านเดิม">
            </div>
            <div class="mb-3">
                <label class="block font-medium mb-1">รหัสผ่านใหม่</label>
                <input type="password" id="new-pwd" placeholder="ป้อนรหัสผ่านใหม่">
            </div>
            <div class="mb-4">
                <label class="block font-medium mb-1">ยืนยันรหัสผ่านใหม่</label>
                <input type="password" id="confirm-pwd" placeholder="ป้อนรหัสผ่านใหม่อีกครั้ง">
            </div>
            <button class="btn btn-primary" onclick="changePassword()">บันทึกรหัสผ่าน</button>
        </div>
    </div>
</div>

<!-- ====== แดชบอร์ด ====== -->
<div id="page-dashboard" class="page">
    <div class="flex justify-between items-center mb-6">
        <h1 class="text-2xl font-bold">📊 แดชบอร์ดผลการประเมิน</h1>
        <button class="btn btn-gray" onclick="showPage('admin')">⚙️ จัดการระบบ</button>
    </div>

    <div class="card mb-6">
        <div class="grid grid-cols-1 md:grid-cols-4 gap-4">
            <div><label class="block font-medium mb-1">วันที่เริ่ม</label><input type="date" id="f-start"></div>
            <div><label class="block font-medium mb-1">วันที่สิ้นสุด</label><input type="date" id="f-end"></div>
            <div><label class="block font-medium mb-1">คลินิก</label><select id="f-clinic"><option value="">-- ทุกคลินิก --</option></select></div>
            <div class="flex items-end"><button class="btn btn-primary w-full" onclick="applyFilter()">🔍 แสดงผล</button></div>
        </div>
        <div class="mt-3 flex gap-2">
            <button class="btn btn-success" onclick="exportData()">📤 ส่งออก CSV</button>
            <button class="btn btn-gray" onclick="compareClinics()">📊 เปรียบเทียบคลินิก</button>
        </div>
    </div>

    <div class="grid grid-cols-1 md:grid-cols-4 gap-4 mb-6">
        <div class="card text-center"><p class="text-gray-500">จำนวนประเมิน</p><p class="text-2xl font-bold text-blue-600" id="stat-total">0</p></div>
        <div class="card text-center"><p class="text-gray-500">คะแนนเฉลี่ยรวม</p><p class="text-2xl font-bold text-green-600" id="stat-avg">0.00</p></div>
        <div class="card text-center"><p class="text-gray-500">ร้อยละความพึงพอใจ</p><p class="text-2xl font-bold text-amber-600" id="stat-pct">0%</p></div>
        <div class="card text-center"><p class="text-gray-500">เป้าหมาย ≥ 85%</p><p class="text-2xl font-bold" id="stat-goal">--</p></div>
    </div>

    <div class="grid grid-cols-1 md:grid-cols-2 gap-6 mb-6">
        <div class="card"><h3 class="font-bold mb-3">คะแนนแยกตามหมวด SMILE</h3><canvas id="chart-smile" height="250"></canvas></div>
        <div class="card"><h3 class="font-bold mb-3">เปรียบเทียบคลินิก</h3><canvas id="chart-clinics" height="250"></canvas></div>
    </div>
</div>

</div>

<script>
// === ข้อมูลเริ่มต้น ===
const DEFAULT_CLINICS = [
    "ห้องฉุกเฉิน", "ไตเทียม", "หน่วยบำบัดระยะสั้น", "จุดคัดกรอง",
    "คลินิกเวชศาสตร์ครอบครัว", "คลินิกเลเซอร์ผิวหนัง", "คลินิกเวชศาสตร์ฟื้นฟู",
    "คลินิกบุคลากรและนิสิต", "คลินิกอายุรกรรม", "คลินิกส่องกล้องอายุรกรรม",
    "คลินิกห้องทำหัตถการผู้ป่วยนอก", "คลินิกรังสีวินิจฉัย", "คลินิกรังสีรักษา",
    "คลินิกรังสีร่วมรักษา", "คลินิกตรวจสุขภาพ", "คลินิกดูแลแผลและทวารเทียม",
    "คลินิกออร์โธปิดิกส์ (กระดูกและข้อ)", "คลินิกสูติ-นรีเวชกรรม",
    "คลินิกโรคหัวใจ", "คลินิกศัลยกรรม", "คลินิกกุมารเวชกรรม",
    "คลินิกโสต ศอ นาสิก", "คลินิกจิตเวช", "ศูนย์เปล"
];

const DEFAULT_QUESTIONS = [
    {id:"S1", cat:"S", text:"ได้รับการต้อนรับด้วยรอยยิ้ม น้ำเสียงสุภาพ และทักทายด้วยความสุภาพอย่างสม่ำเสมอ"},
    {id:"S2", cat:"S", text:"เรียกชื่อ-นามสกุลท่านถูกต้องด้วยความเคารพ"},
    {id:"S3", cat:"S", text:"พูดจาให้เกียรติ แจ้งขั้นตอนชัดเจน ไม่ทำให้รู้สึกถูกลดความสำคัญ"},
    {id:"M1", cat:"M", text:"เลือกใช้คำพูดสุภาพ เหมาะสม ไม่ทำให้รู้สึกเสียใจหรือไม่ได้รับความเคารพ"},
    {id:"M2", cat:"M", text:"แจ้งเวลารอคอยและขั้นตอนชัดเจน อธิบายจนเข้าใจ ไม่รีบเร่ง"},
    {id:"I1", cat:"I", text:"พูดความจริง รักษาความลับ ปฏิบัติตามที่สัญญา ด้วยความจริงใจ"},
    {id:"L1", cat:"L", text:"รับฟังท่านจนจบ ไม่ขัดจังหวะ ให้ถามได้เต็มที่ เข้าใจความรู้สึก"},
    {id:"E1", cat:"E", text:"ปฏิบัติต่อท่านเท่าเทียมกัน ไม่แบ่งอายุ สิทธิ์ หรือสถานภาพ"},
    {id:"E2", cat:"E", text:"เอาใจใส่ ดูแลทั่วถึง ให้เวลาเพียงพอ ไม่ทอดทิ้ง"}
];

// === จัดการรหัสผ่าน - ซ่อนและเปลี่ยนได้ ===
function getAdminPassword() {
    const stored = localStorage.getItem('admin_password');
    return stored ? stored : 'admin123'; // ค่าเริ่มต้นครั้งแรก ไม่แสดงที่หน้าเว็บ
}

function setAdminPassword(newPwd) {
    localStorage.setItem('admin_password', newPwd);
}

let CLINICS = JSON.parse(localStorage.getItem('smile_clinics')) || DEFAULT_CLINICS;
let QUESTIONS = JSON.parse(localStorage.getItem('smile_questions')) || DEFAULT_QUESTIONS;
let RESPONSES = JSON.parse(localStorage.getItem('smile_responses')) || [];
let isAdmin = false;
let answers = {};
let chartSmile = null, chartClinics = null;

// สลับหน้า
function showPage(name) {
    document.querySelectorAll('.page').forEach(p => p.classList.remove('active'));
    document.getElementById(`page-${name}`).classList.add('active');
    document.getElementById('logout-btn').classList.toggle('hidden', !isAdmin);
    if (name === 'survey') { resetSurvey(); }
    if (name === 'admin') { renderClinicList(); renderQuestionList(); updateDataList(); }
    if (name === 'dashboard') { updateDashboard(); populateFilterClinics(); }
}

// === เปลี่ยนรหัสผ่าน ===
function changePassword() {
    const current = document.getElementById('current-pwd').value;
    const newPwd = document.getElementById('new-pwd').value;
    const confirm = document.getElementById('confirm-pwd').value;
    
    if (current !== getAdminPassword()) {
        alert('รหัสผ่านเดิมไม่ถูกต้อง');
        return;
    }
    if (!newPwd || newPwd.length < 4) {
        alert('รหัสผ่านต้องมีอย่างน้อย 4 ตัวอักษร');
        return;
    }
    if (newPwd !== confirm) {
        alert('รหัสผ่านใหม่ไม่ตรงกัน');
        return;
    }
    
    setAdminPassword(newPwd);
    alert('เปลี่ยนรหัสผ่านเรียบร้อยแล้ว');
    
    // ล้างช่อง
    document.getElementById('current-pwd').value = '';
    document.getElementById('new-pwd').value = '';
    document.getElementById('confirm-pwd').value = '';
}

// === คลินิก ===
function saveClinics() { localStorage.setItem('smile_clinics', JSON.stringify(CLINICS)); }
function addClinic() {
    const name = document.getElementById('new-clinic').value.trim();
    if (!name) return alert('ป้อนชื่อคลินิก');
    if (CLINICS.includes(name)) return alert('มีชื่อนี้แล้ว');
    CLINICS.push(name); saveClinics(); renderClinicList(); populateSurveyClinics(); populateFilterClinics();
    document.getElementById('new-clinic').value = '';
}
function removeClinic(name) {
    if (!confirm(`ลบ "${name}" ใช่ไหม`)) return;
    CLINICS = CLINICS.filter(c => c !== name); saveClinics(); renderClinicList(); populateSurveyClinics(); populateFilterClinics();
}
function renderClinicList() {
    document.getElementById('clinic-list').innerHTML = CLINICS.map(c => 
        `<span class="inline-block bg-sky-100 text-sky-800 px-3 py-1 rounded-full text-sm mr-2 mb-2">${c} <span class="text-red-500 cursor-pointer" onclick="removeClinic('${c}')">×</span></span>`
    ).join('');
}
function populateSurveyClinics() {
    document.getElementById('s-clinic').innerHTML = '<option value="">-- เลือกคลินิก --</option>' + CLINICS.map(c => `<option>${c}</option>`).join('');
}
function populateFilterClinics() {
    document.getElementById('f-clinic').innerHTML = '<option value="">-- ทุกคลินิก --</option>' + CLINICS.map(c => `<option>${c}</option>`).join('');
}

// === ข้อคำถาม ===
function saveQuestions() { localStorage.setItem('smile_questions', JSON.stringify(QUESTIONS)); }
function addQuestion() {
    const cat = document.getElementById('q-cat').value;
    const text = document.getElementById('q-text').value.trim();
    if (!text) return alert('ป้อนข้อความคำถาม');
    QUESTIONS.push({id: cat + (QUESTIONS.filter(q => q.cat === cat).length + 1), cat, text});
    saveQuestions(); renderQuestionList(); renderSurveyQuestions();
    document.getElementById('q-text').value = '';
}
function removeQuestion(id) {
    if (!confirm('ลบข้อคำถามนี้?')) return;
    QUESTIONS = QUESTIONS.filter(q => q.id !== id); saveQuestions();
    renderQuestionList(); renderSurveyQuestions();
}
function renderQuestionList() {
    document.getElementById('question-list').innerHTML = QUESTIONS.map(q => `
        <div class="p-3 border-b flex justify-between items-center">
            <div><strong>${q.id}</strong> — ${q.text}</div>
            <button class="btn btn-danger text-xs py-1" onclick="removeQuestion('${q.id}')">ลบ</button>
        </div>`).join('');
}
function renderSurveyQuestions() {
    const container = document.getElementById('questions-container');
    container.innerHTML = QUESTIONS.map(q => `
        <div class="py-4 border-b" data-qid="${q.id}">
            <p class="mb-3 font-medium">${q.id}. ${q.text}</p>
            <div class="face-container">
                <span class="face-option face-5" data-v="5">😊<br><span class="text-xs">ดีมาก</span></span>
                <span class="face-option face-4" data-v="4">🙂<br><span class="text-xs">ดี</span></span>
                <span class="face-option face-3" data-v="3">😐<br><span class="text-xs">ปานกลาง</span></span>
                <span class="face-option face-2" data-v="2">🙁<br><span class="text-xs">ปรับปรุง</span></span>
                <span class="face-option face-1" data-v="1">😞<br><span class="text-xs">แก้ไข</span></span>
            </div>
        </div>`).join('');
    
    container.querySelectorAll('.face-option').forEach(f => {
        f.addEventListener('click', function() {
            const row = this.closest('[data-qid]');
            row.querySelectorAll('.face-option').forEach(x => x.classList.remove('selected'));
            this.classList.add('selected');
            answers[row.getAttribute('data-qid')] = parseInt(this.getAttribute('data-v'));
        });
    });
}

// === แบบประเมิน ===
function resetSurvey() {
    answers = {};
    document.getElementById('survey-success').classList.add('hidden');
    ['s-type','s-clinic','s-right','s-age','s-impress','s-improve','s-suggest'].forEach(id => {
        document.getElementById(id).value = '';
    });
    renderSurveyQuestions();
    populateSurveyClinics();
}
function submitSurvey() {
    const type = document.getElementById('s-type').value;
    const clinic = document.getElementById('s-clinic').value;
    const right = document.getElementById('s-right').value;
    const age = document.getElementById('s-age').value;
    
    if (!type || !clinic || !right || !age) return alert('กรุณาเลือกข้อมูลส่วนที่ 1 ให้ครบถ้วน');
    if (QUESTIONS.some(q => answers[q.id] === undefined)) return alert('กรุณาตอบทุกข้อคำถามด้วยรูปหน้า');
    
    const scores = Object.values(answers);
    const avg = scores.reduce((a,b) => a+b, 0) / scores.length;
    const refId = 'SMILE-' + new Date().toISOString().slice(0,10).replace(/-/g,'') + '-' + Math.random().toString(36).substr(2,4).toUpperCase();
    
    RESPONSES.push({
        id: refId, date: new Date().toISOString().slice(0,10),
        type, clinic, right, age,
        answers: {...answers}, avg: avg.toFixed(2),
        impress: document.getElementById('s-impress').value,
        improve: document.getElementById('s-improve').value,
        suggest: document.getElementById('s-suggest').value
    });
    localStorage.setItem('smile_responses', JSON.stringify(RESPONSES));
    
    document.getElementById('ref-code').textContent = refId;
    document.getElementById('survey-success').classList.remove('hidden');
}

// === เข้าสู่ระบบ ===
function adminLogin() {
    const inputPwd = document.getElementById('admin-pwd').value;
    if (inputPwd === getAdminPassword()) {
        isAdmin = true;
        document.getElementById('nav-links').classList.add('hidden');
        showPage('dashboard');
    } else {
        alert('รหัสผ่านไม่ถูกต้อง');
    }
}

function logout() {
    isAdmin = false;
    document.getElementById('logout-btn').classList.add('hidden');
    document.getElementById('nav-links').classList.remove('hidden');
    document.getElementById('admin-pwd').value = '';
    showPage('survey');
}

function showAdminTab(name) {
    document.querySelectorAll('.admin-tab').forEach(t => t.classList.add('hidden'));
    document.getElementById(`tab-${name}`).classList.remove('hidden');
    document.querySelectorAll('.tab-btn').forEach(b => b.classList.remove('active'));
    event.target.classList.add('active');
}

function updateDataList() {
    document.getElementById('total-count').textContent = RESPONSES.length;
    document.getElementById('all-data-list').innerHTML = RESPONSES.slice().reverse().map(r => `
        <tr class="border-b">
            <td class="p-2">${r.date}</td>
            <td class="p-2">${r.id}</td>
            <td class="p-2">${r.clinic}</td>
            <td class="p-2 font-bold">${r.avg}</td>
            <td class="p-2">${getLevel(r.avg)}</td>
        </tr>`).join('');
}
function getLevel(avg) {
    const n = parseFloat(avg);
    if (n >= 4.5) return '<span class="text-green-600">🌟 ดีเยี่ยม</span>';
    if (n >= 3.8) return '<span class="text-blue-600">✅ ดี</span>';
    if (n >= 2.5) return '<span class="text-yellow-600">⚠️ ปานกลาง</span>';
    return '<span class="text-red-600">🔴 ปรับปรุง</span>';
}

// === แดชบอร์ด ===
function getFilteredData() {
    let data = [...RESPONSES];
    const s = document.getElementById('f-start').value;
    const e = document.getElementById('f-end').value;
    const c = document.getElementById('f-clinic').value;
    if (s) data = data.filter(d => d.date >= s);
    if (e) data = data.filter(d => d.date <= e);
    if (c) data = data.filter(d => d.clinic === c);
    return data;
}
function applyFilter() { updateDashboard(); }
function updateDashboard() {
    const d = getFilteredData();
    if (!d.length) {
        document.getElementById('stat-total').textContent = '0';
        document.getElementById('stat-avg').textContent = '0.00';
        document.getElementById('stat-pct').textContent = '0%';
        return;
    }
    const avgAll = d.reduce((s,x) => s + parseFloat(x.avg), 0) / d.length;
    const pct = ((avgAll/5)*100).toFixed(1);
    
    document.getElementById('stat-total').textContent = d.length;
    document.getElementById('stat-avg').textContent = avgAll.toFixed(2);
    document.getElementById('stat-pct').textContent = pct + '%';
    document.getElementById('stat-goal').textContent = avgAll >= 3.8 ? '✅ ผ่าน' : '🔴 ไม่ผ่าน';
    document.getElementById('stat-goal').className = 'text-2xl font-bold ' + (avgAll >= 3.8 ? 'text-green-600' : 'text-red-600');
    
    const cats = ['S','M','I','L','E'];
    const names = {S:'ยิ้ม-ทักทาย',M:'ไตร่ตรอง',I:'ซื่อสัตย์',L:'รับฟัง',E:'เท่าเทียม'};
    const scores = cats.map(cat => {
        const qs = QUESTIONS.filter(q => q.cat === cat);
        let s=0, n=0;
        d.forEach(r => { qs.forEach(q => { if (r.answers[q.id]) { s += r.answers[q.id]; n++; }}); });
        return n ? (s/n).toFixed(2) : 0;
    });
    renderChart('chart-smile', chartSmile, cats.map(c=>names[c]), scores);
    chartSmile = window.renderedChart;
    
    const cData = {};
    d.forEach(r => { if (!cData[r.clinic]) cData[r.clinic] = {s:0,n:0}; cData[r.clinic].s += parseFloat(r.avg); cData[r.clinic].n++; });
    const cLabels = Object.keys(cData);
    const cAvgs = cLabels.map(c => (cData[c].s/cData[c].n).toFixed(2));
    renderChart('chart-clinics', chartClinics, cLabels, cAvgs);
    chartClinics = window.renderedChart;
}
function renderChart(canvasId, oldChart, labels, data, label='คะแนน') {
    if (oldChart) oldChart.destroy();
    const ctx = document.getElementById(canvasId).getContext('2d');
    window.renderedChart = new Chart(ctx, {
        type: 'bar',
        data: {
            labels: labels,
            datasets: [{
                label: label,
                data: data,
                backgroundColor: ['#16a34a','#22c55e','#eab308','#f97316','#ef4444'],
                borderRadius: 6
            }]
        },
        options: { responsive: true, scales: { y: { beginAtZero: true, max: 5 } } }
    });
}
function compareClinics() {
    document.getElementById('f-clinic').value = '';
    updateDashboard();
}
function exportData() {
    const d = getFilteredData();
    if (!d.length) return alert('ไม่มีข้อมูล');
    const headers = 'วันที่,รหัส,ประเภท,คลินิก,สิทธิ์,ช่วงอายุ,คะแนนเฉลี่ย,ประทับใจ,ปรับปรุง,เสนอแนะ';
    const rows = d.map(r => [
        r.date, r.id, r.type, r.clinic, r.right, r.age, r.avg,
        `"${(r.impress||'').replace(/"/g,'""')}"`,
        `"${(r.improve||'').replace(/"/g,'""')}"`,
        `"${(r.suggest||'').replace(/"/g,'""')}"`
    ].join(','));
    const csv = [headers, ...rows].join('\n');
    const blob = new Blob(['\uFEFF'+csv], {type:'text/csv;charset=utf-8'});
    const url = URL.createObjectURL(blob);
    const a = document.createElement('a');
    a.href = url; a.download = `smile-export-${new Date().toISOString().slice(0,10)}.csv`;
    a.click();
}

document.addEventListener('DOMContentLoaded', () => {
    populateSurveyClinics();
    renderSurveyQuestions();
});
</script>
</body>
</html>
