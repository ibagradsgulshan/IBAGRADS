<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>A-Level & O-Level Equivalence Calculator</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
    <script src="https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
    <style>
        body {
            font-family: 'Inter', sans-serif;
        }
        /* Custom styles for a subtle background pattern */
        body::before {
            content: '';
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-image: radial-gradient(circle at top left, rgba(192, 132, 252, 0.1), transparent 30%),
                              radial-gradient(circle at bottom right, rgba(129, 140, 248, 0.1), transparent 30%);
            z-index: -1;
        }
    </style>
</head>
<body class="bg-slate-50 text-slate-800">

    <div class="container mx-auto p-4 sm:p-6 md:p-8 max-w-4xl">
        <header class="text-center mb-8">
            <img src="https://placehold.co/300x80/000000/FFFFFF?text=IBA+GRADS" alt="IBA Grads Logo" class="mx-auto mb-4 h-16 w-auto">
            <h1 class="text-3xl sm:text-4xl font-bold text-slate-900">Grade Equivalence Calculator</h1>
            <p class="text-slate-600 mt-2">Apne O-Level aur A-Level grades daalein aur equivalence percentage maloom karein.</p>
        </header>

        <main class="space-y-8">
            <section id="o-levels" class="bg-white/80 backdrop-blur-sm p-6 rounded-2xl shadow-lg border border-slate-200">
                <h2 class="text-2xl font-semibold mb-4 text-indigo-600">O-Level / IGCSE Grades</h2>
                <div id="o-level-subjects-container" class="grid grid-cols-1 sm:grid-cols-2 gap-4">
                    </div>
            </section>

            <section id="a-levels" class="bg-white/80 backdrop-blur-sm p-6 rounded-2xl shadow-lg border border-slate-200">
                <h2 class="text-2xl font-semibold mb-4 text-purple-600">A-Level Grades</h2>
                <div id="a-level-subjects-container" class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 gap-4">
                    </div>
            </section>

            <section class="text-center" id="action-section">
                <button id="calculate-btn" class="bg-indigo-600 text-white font-bold py-3 px-8 rounded-lg hover:bg-indigo-700 focus:outline-none focus:ring-4 focus:ring-indigo-300 transition-all duration-300 transform hover:scale-105 shadow-md">
                    Calculate Equivalence
                </button>

                <div id="result-container" class="mt-8 p-6 bg-green-100 border-2 border-green-300 rounded-2xl text-green-800 text-center hidden animate-fade-in">
                    <p class="text-lg">Aapki equivalence percentage hai:</p>
                    <p id="result-percentage" class="text-4xl font-bold my-2">0%</p>
                </div>

                <div id="error-message" class="mt-6 p-4 bg-red-100 border border-red-300 rounded-lg text-red-700 text-center hidden">
                    Please select a grade for all subjects.
                </div>
                
                <button id="export-pdf-btn" class="hidden mt-4 bg-green-600 text-white font-bold py-3 px-8 rounded-lg hover:bg-green-700 focus:outline-none focus:ring-4 focus:ring-green-300 transition-all duration-300 transform hover:scale-105 shadow-md">
                    📄 Export as PDF
                </button>
            </section>
            
            <footer class="text-center mt-8">
                <p class="text-xs text-slate-500 px-4">
                    “This calculation/report is only for your assistance. For confirmation, only IBCC can provide the final equivalence.”
                </p>
            </footer>
        </main>
    </div>

    <script>
        document.addEventListener('DOMContentLoaded', function () {
            const oLevelContainer = document.getElementById('o-level-subjects-container');
            const aLevelContainer = document.getElementById('a-level-subjects-container');
            const calculateBtn = document.getElementById('calculate-btn');
            const resultContainer = document.getElementById('result-container');
            const resultPercentage = document.getElementById('result-percentage');
            const errorMessage = document.getElementById('error-message');
            // NEW: Get export button
            const exportPdfBtn = document.getElementById('export-pdf-btn');

            const gradePoints = {
                'A*': 90, 'A': 85, 'B': 75, 'C': 65, 'D': 55, 'E': 45, 'U': 0, '--': -1
            };
            const aLevelGradePoints = {
                'A*': 90, 'A': 85, 'B': 75, 'C': 65, 'D': 55, 'E': 45, 'U': 0, '--': -1
            };

            const grades = Object.keys(gradePoints);
            const aLevelGrades = Object.keys(aLevelGradePoints);

            function createSubjectInput(level, index) {
                const subjectDiv = document.createElement('div');
                subjectDiv.className = 'flex flex-col space-y-1';
                const label = document.createElement('label');
                label.className = 'text-sm font-medium text-slate-700';
                label.textContent = `Subject ${index + 1}`;
                const select = document.createElement('select');
                select.className = `${level}-grade-selector w-full p-2 border border-slate-300 rounded-lg focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 transition`;
                const currentGrades = level === 'o-level' ? grades : aLevelGrades;
                currentGrades.forEach(grade => {
                    const option = document.createElement('option');
                    option.value = grade;
                    option.textContent = grade === '--' ? 'Select Grade' : grade;
                    select.appendChild(option);
                });
                subjectDiv.appendChild(label);
                subjectDiv.appendChild(select);
                return subjectDiv;
            }

            for (let i = 0; i < 8; i++) {
                oLevelContainer.appendChild(createSubjectInput('o-level', i));
            }
            for (let i = 0; i < 3; i++) {
                aLevelContainer.appendChild(createSubjectInput('a-level', i));
            }

            calculateBtn.addEventListener('click', function () {
                let totalPoints = 0;
                let totalSubjects = 0;
                let allSelected = true;

                resultContainer.classList.add('hidden');
                errorMessage.classList.add('hidden');
                exportPdfBtn.classList.add('hidden'); // Hide on new calculation

                const oLevelSelectors = document.querySelectorAll('.o-level-grade-selector');
                oLevelSelectors.forEach(select => {
                    const grade = select.value;
                    if (grade === '--') { allSelected = false; return; }
                    totalPoints += gradePoints[grade];
                    totalSubjects++;
                });

                const aLevelSelectors = document.querySelectorAll('.a-level-grade-selector');
                aLevelSelectors.forEach(select => {
                    const grade = select.value;
                    if (grade === '--') { allSelected = false; return; }
                    totalPoints += aLevelGradePoints[grade];
                    totalSubjects++;
                });

                if (!allSelected || totalSubjects < 11) {
                    errorMessage.textContent = 'Har subject ke liye grade select karna zaroori hai.';
                    errorMessage.classList.remove('hidden');
                    return;
                }
                
                const totalPossibleMarks = 1100;
                const percentage = (totalPoints / totalPossibleMarks) * 100;
                
                resultPercentage.textContent = `${percentage.toFixed(2)}%`;
                resultContainer.classList.remove('hidden');
                exportPdfBtn.classList.remove('hidden'); // Show export button on success
            });

            // NEW: PDF EXPORT FUNCTIONALITY
            exportPdfBtn.addEventListener('click', function () {
                const { jsPDF } = window.jspdf;
                const reportContent = document.querySelector('main');
                const actionSection = document.getElementById('action-section');

                // Temporarily hide the buttons for a cleaner PDF
                actionSection.style.visibility = 'hidden';

                html2canvas(reportContent, {
                    scale: 2 // Improve image quality
                }).then(canvas => {
                    // Make buttons visible again after the capture
                    actionSection.style.visibility = 'visible';

                    const imgData = canvas.toDataURL('image/png');
                    
                    // A4 dimensions: 210mm wide, 297mm high
                    const pdf = new jsPDF({
                        orientation: 'p',
                        unit: 'mm',
                        format: 'a4'
                    });

                    const pdfWidth = pdf.internal.pageSize.getWidth();
                    const pdfHeight = pdf.internal.pageSize.getHeight();
                    const canvasWidth = canvas.width;
                    const canvasHeight = canvas.height;
                    
                    // Calculate the aspect ratio to fit the image correctly
                    const ratio = canvasHeight / canvasWidth;
                    const imgWidth = pdfWidth - 20; // 10mm margin on each side
                    const imgHeight = imgWidth * ratio;

                    // Add image to PDF
                    pdf.addImage(imgData, 'PNG', 10, 10, imgWidth, imgHeight);
                    pdf.save('Equivalence-Report.pdf');
                });
            });
        });
    </script>

</body>
</html>
