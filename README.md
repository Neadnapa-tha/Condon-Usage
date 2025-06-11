# Codon Usage Analysis

การวิเคราะห์ความถี่การใช้โคดอน (Codon Usage) ในส่วน coding ของ DNA จากสิ่งมีชีวิตหลากหลายกลุ่ม โดยใช้ข้อมูลจากฐานข้อมูล **CUTG (Codon Usage Tabulated from GenBank)** ซึ่งประกอบด้วยข้อมูลของสิ่งมีชีวิตจากหลายอาณาจักร (kingdoms) ทั้งในระดับจีโนมและพลาสมิด

---

## 🧬 Dataset Description

- **CUGT**: ความถี่ของการใช้งานโคดอนในรูปแบบเลขทศนิยม 5 หลัก (float)
- **NCODONS**: จำนวนโคดอนที่ใช้
- **SPECIESNAME**: ชื่อสิ่งมีชีวิต
- **SPECIESID**: รหัสของสิ่งมีชีวิต
- **KINGDOM**: อาณาจักรสิ่งมีชีวิตที่จัดกลุ่มไว้จำนวน 11 ประเภท:
    - `'arc'`: Archaea
    - `'bct'`, `'phg'`: Bacteria
    - `'plm'`: Plasmid
    - `'pln'`: พืช
    - `'inv'`: สัตว์ไม่มีกระดูกสันหลัง
    - `'vrt'`: สัตว์มีกระดูกสันหลัง
    - `'mam'`: สัตว์เลี้ยงลูกด้วยนม
    - `'rod'`: สัตว์ฟันแทะ
    - `'pri'`: ไพรเมต
    - `'vrl'`: ไวรัส
- **DNATYPE**: ประเภทของ DNA (0-12) เช่น
    - `0`: genomic
    - `1`: mitochondrial
    - `2`: chloroplast
    - ... ถึง `12`: kinetoplast

---

## 🧹 Data Preparation

### 🔧 Cleaning
- ลบค่าที่เป็น `0` และ `null` ออกจาก CUGT เนื่องจากอาจเป็นค่าผิดปกติ
- แปลงค่าใน column ที่ควรเป็นตัวเลข (เช่น `UUU`, `UUC`) จาก object เป็น numerical

### 📊 Splitting
- แบ่งข้อมูลเป็น 3 ส่วน:
    - Train: 60%
    - Validation: 20%
    - Test: 20%

---

## ⚙️ Data Preprocessing

- **Encoding**: แปลง `Kingdom` ให้เป็น numerical เพื่อใช้ในการพยากรณ์
- **Feature Selection**: ใช้ ANOVA ในการเลือกคุณลักษณะสำคัญที่ส่งผลต่อการพยากรณ์
- **Feature Engineering**:
    - Feature scaling
    - Feature encoding
    - การเปรียบเทียบระหว่าง all features และ selected features

---

## 🤖 Model Selection

ทำการเปรียบเทียบโมเดลและปรับแต่งพารามิเตอร์โดยใช้ Validation set เพื่อหลีกเลี่ยง Overfitting/Underfitting

### โมเดลที่ใช้:
- **Logistic Regression** + Polynomial Features
- **Random Forest**
- **Decision Tree**

### การประเมิน:
- **Regression**: MSE, RMSE, MAE, MAPE
- **Classification**: Accuracy, Precision, Recall, F1-score

### ผลลัพธ์จาก Learning Curve:
- **All Features**:
    - Logistic Regression: ไม่มี overfitting/underfitting
    - Random Forest: มีลักษณะ underfitting ในช่วงแรก แต่ปรับตัวดี
    - Decision Tree: เริ่มต้นมี underfitting แล้วปรับเข้าสู่เสถียร
- **Selected Features**:
    - Logistic Regression: Learning curve ขนานดี แต่ accuracy ยังต่ำ
    - Random Forest: valid accuracy ต่ำและคงที่
    - Decision Tree: เกิด overfitting

---

## 🧪 Evaluation on Test Set

เมื่อนำโมเดลที่ดีที่สุด (**Logistic Regression + Polynomial Features (degree = 3)**) มาทดสอบกับ Test Set พบว่าได้ **Accuracy = 0.93** ซึ่งถือว่ามีประสิทธิภาพดีมาก
