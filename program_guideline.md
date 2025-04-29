Tôi hiểu bạn muốn một hướng dẫn **từng bước chi tiết** để sinh viên (người mới, cần hướng dẫn cụ thể) có thể tạo và phát triển dự án quản lý nhân sự trên NetBeans, bao gồm GUI (dùng Swing), từ việc tạo project đến vẽ các form giao diện. Hướng dẫn sẽ dựa trên **Project Structure** và **Project Design Document** đã cung cấp, tập trung vào việc đạt 95% chức năng của dự án mẫu (`sample.txt`) với các chức năng: đăng nhập, CRUD nhân viên, tìm kiếm, thống kê, và lưu trữ XML. Tôi sẽ giả định NetBeans đã được cài (qua Ubuntu Store) và sinh viên dùng JDK 17/21. Hướng dẫn sẽ rất cụ thể, dễ hiểu, với các bước tạo project, vẽ form GUI bằng NetBeans GUI Builder, và triển khai code.

---

### **Hướng dẫn từng bước phát triển dự án Quản lý nhân sự trên NetBeans**

#### **Mục tiêu**
- Tạo dự án Java Application (Ant) trên NetBeans.
- Xây dựng GUI (dùng Swing) cho đăng nhập, quản lý nhân viên, và thêm/sửa nhân viên.
- Triển khai các chức năng: đăng nhập, CRUD, tìm kiếm, thống kê, lưu trữ XML.
- Đảm bảo sinh viên mới có thể làm theo mà không bị rối.

#### **Chuẩn bị**
- NetBeans đã cài (giả định phiên bản 18 hoặc 21).
- JDK 17 hoặc 21 được cấu hình trong NetBeans.
- Sinh viên có thể mở NetBeans và bắt đầu ngay.

---

### **Tuần 1: Tạo dự án và cấu trúc cơ bản**

#### **Bước 1: Tạo dự án mới**
1. Mở NetBeans.
2. Vào `File > New Project`.
3. Trong cửa sổ hiện ra:
   - Chọn **Java with Ant** > **Java Application**.
   - Nhấn `Next`.
4. Cấu hình dự án:
   - **Project Name**: `QLNS`.
   - **Project Location**: Chọn thư mục (ví dụ: `~/Projects/QLNS`).
   - **Bỏ chọn** ô `Create Main Class` (chúng ta sẽ tạo sau).
   - Nhấn `Finish`.
5. Kết quả: Trong tab `Projects`, bạn thấy dự án `QLNS` với thư mục `Source Packages`.

#### **Bước 2: Tạo cấu trúc package**
1. Trong tab `Projects`, right-click lên `Source Packages` > `New > Java Package`.
2. Tạo các package sau (nhập tên và nhấn `OK`):
   - `com.mycompany.qlns.controller`
   - `com.mycompany.qlns.dao`
   - `com.mycompany.qlns.model`
   - `com.mycompany.qlns.utils`
   - `com.mycompany.qlns.view`
3. Kết quả: Bạn thấy các package trong `Source Packages`.

#### **Bước 3: Tạo thư mục XML**
1. Chuyển sang tab `Files` (bên cạnh tab `Projects`).
2. Right-click lên thư mục dự án `QLNS` > `New > Folder`.
3. Đặt tên: `XML` > Nhấn `Finish`.
4. Kết quả: Thư mục `XML` xuất hiện trong `QLNS`.

#### **Bước 4: Thêm thư viện JAXB (cho XML)**
1. Right-click dự án `QLNS` trong tab `Projects` > `Properties`.
2. Trong cửa sổ hiện ra, chọn `Libraries` > Nhấn `Add JAR/Folder`.
3. Tải các file JAR của JAXB (tìm trên Maven Repository hoặc GitHub, ví dụ: `jaxb-api`, `jaxb-impl`, `jaxb-core`).
4. Chọn các file JAR đã tải > Nhấn `Open` > Nhấn `OK`.
5. Nếu chưa có JAR, tạm bỏ qua bước này (sẽ thêm sau khi cần đọc/ghi XML).

#### **Bước 5: Tạo lớp `Employee`**
1. Trong tab `Projects`, right-click lên package `com.mycompany.qlns.model` > `New > Java Class`.
2. Đặt tên: `Employee` > Nhấn `Finish`.
3. Copy đoạn code sau vào `Employee.java`:

```java
package com.mycompany.qlns.model;

import java.time.LocalDate;
import java.time.Period;
import javax.xml.bind.annotation.XmlAccessType;
import javax.xml.bind.annotation.XmlAccessorType;
import javax.xml.bind.annotation.XmlElement;
import javax.xml.bind.annotation.XmlRootElement;
import javax.xml.bind.annotation.adapters.XmlJavaTypeAdapter;
import com.mycompany.qlns.utils.LocalDateAdapter;

@XmlRootElement(name = "employee")
@XmlAccessorType(XmlAccessType.FIELD)
public class Employee {
    @XmlElement
    private int id;
    @XmlElement
    private String name;
    @XmlElement
    @XmlJavaTypeAdapter(LocalDateAdapter.class)
    private LocalDate birthDate;
    @XmlElement
    private String gender;
    @XmlElement
    private String role;
    @XmlElement
    private double salary;
    private static int nextId = 1;

    public Employee() {}

    public Employee(int id, String name, LocalDate birthDate, String gender, String role, double salary) {
        this.id = id;
        this.name = name;
        this.birthDate = birthDate;
        this.gender = gender;
        this.role = role;
        this.salary = salary;
        if (id >= nextId) {
            nextId = id + 1;
        }
    }

    public int getId() { return id; }
    public String getName() { return name; }
    public LocalDate getBirthDate() { return birthDate; }
    public String getGender() { return gender; }
    public String getRole() { return role; }
    public double getSalary() { return salary; }
    public static int getNextId() { return nextId; }

    public void setId(int id) { this.id = id; }
    public void setName(String name) { this.name = name; }
    public void setBirthDate(LocalDate birthDate) { this.birthDate = birthDate; }
    public void setGender(String gender) { this.gender = gender; }
    public void setRole(String role) { this.role = role; }
    public void setSalary(double salary) { this.salary = salary; }

    public int getAge() {
        return Period.between(birthDate, LocalDate.now()).getYears();
    }

    @Override
    public String toString() {
        return "Employee{id=" + id + ", name=" + name + ", age=" + getAge() + ", gender=" + gender + ", role=" + role + ", salary=" + salary + "}";
    }
}
```

4. Lưu file (`Ctrl + S`).

#### **Bước 6: Tạo lớp `LocalDateAdapter`**
1. Right-click lên package `com.mycompany.qlns.utils` > `New > Java Class`.
2. Đặt tên: `LocalDateAdapter` > Nhấn `Finish`.
3. Copy đoạn code sau:

```java
package com.mycompany.qlns.utils;

import java.time.LocalDate;
import java.time.format.DateTimeFormatter;
import javax.xml.bind.annotation.adapters.XmlAdapter;

public class LocalDateAdapter extends XmlAdapter<String, LocalDate> {
    private static final DateTimeFormatter FORMATTER = DateTimeFormatter.ofPattern("yyyy-MM-dd");

    @Override
    public LocalDate unmarshal(String v) throws Exception {
        return LocalDate.parse(v, FORMATTER);
    }

    @Override
    public String marshal(LocalDate v) throws Exception {
        return v.format(FORMATTER);
    }
}
```

4. Lưu file.

#### **Bước 7: Test nhanh**
1. Right-click lên package `com.mycompany.qlns` > `New > Java Class`.
2. Đặt tên: `Main` > Nhấn `Finish`.
3. Copy code:

```java
package com.mycompany.qlns;

import com.mycompany.qlns.model.Employee;
import java.time.LocalDate;

public class Main {
    public static void main(String[] args) {
        Employee emp = new Employee(1, "Nguyen Van A", LocalDate.of(1995, 5, 20), "Nam", "Nhan vien", 10000000);
        System.out.println(emp);
    }
}
```

4. Nhấn `F6` để chạy. Kiểm tra tab `Output` thấy dòng:
   ```
   Employee{id=1, name=Nguyen Van A, age=30, gender=Nam, role=Nhan vien, salary=10000000.0}
   ```

---

### **Tuần 2: Tạo GUI cho màn hình đăng nhập**

#### **Bước 8: Tạo `LoginView`**
1. Right-click lên package `com.mycompany.qlns.view` > `New > JFrame Form`.
2. Đặt tên: `LoginView` > Nhấn `Finish`.
3. NetBeans mở tab Design cho `LoginView`.
4. Trong cửa sổ **Palette** (bên phải):
   - Kéo 2 **Label** vào form, đặt text:
     - Label 1: `Username:`
     - Label 2: `Password:`
   - Kéo 1 **Text Field** (cho username) đặt cạnh `Username:`.
   - Kéo 1 **Password Field** (cho password) đặt cạnh `Password:`.
   - Kéo 1 **Button** đặt bên dưới, đặt text: `Đăng nhập`.
5. Sắp xếp giao diện:
   - Kéo các thành phần để trông gọn gàng (xem hình mẫu):
     ```
     Username: [Text Field]
     Password: [Password Field]
               [Đăng nhập]
     ```
   - Right-click form > `Set Layout > Absolute Layout` để dễ kéo thả.
6. Đặt tên biến:
   - Click vào Text Field > Trong tab `Properties` (dưới cùng bên phải), đổi `Variable Name` thành `usernameField`.
   - Click vào Password Field > Đổi `Variable Name` thành `passwordField`.
   - Click vào Button > Đổi `Variable Name` thành `loginButton`.
7. Cài đặt tiêu đề và vị trí:
   - Click vào form (phần trống) > Trong `Properties`, tìm `title`, đặt thành `Đăng nhập`.
   - Trong tab `Code`, tìm `Custom Creation Code`, thêm: `setLocationRelativeTo(null)` để form ở giữa màn hình.
8. Thêm sự kiện cho nút Đăng nhập:
   - Double-click vào nút `Đăng nhập` trong tab Design.
   - NetBeans tạo phương thức `loginButtonActionPerformed` trong tab Source.
9. Lưu form (`Ctrl + S`).

#### **Bước 9: Tạo `LoginController`**
1. Right-click lên package `com.mycompany.qlns.controller` > `New > Java Class`.
2. Đặt tên: `LoginController` > Nhấn `Finish`.
3. Copy code:

```java
package com.mycompany.qlns.controller;

import com.mycompany.qlns.view.LoginView;
import com.mycompany.qlns.view.EmployeeView;

public class LoginController {
    private LoginView view;

    public LoginController() {
        view = new LoginView(this);
    }

    public void showLoginView() {
        view.setVisible(true);
    }

    public boolean authenticate(String username, String password) {
        return "admin".equals(username) && "123".equals(password);
    }

    public void showMainView() {
        EmployeeController empController = new EmployeeController();
        EmployeeView empView = new EmployeeView(empController);
        empController.setView(empView);
        empView.setVisible(true);
    }
}
```

4. Lưu file. (Lưu ý: Code này tạm tham chiếu `EmployeeController` và `EmployeeView`, sẽ tạo sau).

#### **Bước 10: Kết nối `LoginView` với `LoginController`**
1. Mở `LoginView.java` trong tab Source.
2. Thêm các thuộc tính và constructor:

```java
private LoginController controller;

public LoginView(LoginController controller) {
    this.controller = controller;
    initComponents();
    setLocationRelativeTo(null);
}
```

3. Sửa phương thức `loginButtonActionPerformed`:

```java
private void loginButtonActionPerformed(java.awt.event.ActionEvent evt) {
    String username = usernameField.getText();
    String password = new String(passwordField.getPassword());
    if (controller.authenticate(username, password)) {
        dispose();
        controller.showMainView();
    } else {
        javax.swing.JOptionPane.showMessageDialog(this, "Sai username hoặc password!", "Lỗi", javax.swing.JOptionPane.ERROR_MESSAGE);
    }
}
```

4. Thêm import ở đầu file:

```java
import com.mycompany.qlns.controller.LoginController;
```

5. Lưu file.

#### **Bước 11: Chạy thử LoginView**
1. Mở `Main.java`, sửa code:

```java
package com.mycompany.qlns;

import com.mycompany.qlns.controller.LoginController;

public class Main {
    public static void main(String[] args) {
        java.awt.EventQueue.invokeLater(() -> {
            new LoginController().showLoginView();
        });
    }
}
```

2. Nhấn `F6` để chạy.
3. Kết quả: Form đăng nhập hiện ra. Nhập `admin`/`123` sẽ báo lỗi (vì chưa có `EmployeeView`). Nhập sai sẽ thấy thông báo lỗi.

---

### **Tuần 3-4: Tạo GUI quản lý nhân viên và logic**

#### **Bước 12: Tạo `EmployeeView`**
1. Right-click lên package `com.mycompany.qlns.view` > `New > JFrame Form`.
2. Đặt tên: `EmployeeView` > Nhấn `Finish`.
3. Trong tab Design, thêm các thành phần:
   - **JTable** (kéo từ Palette vào form): Hiển thị danh sách nhân viên.
   - **JTextField** (2 cái): Tìm kiếm theo tên và tuổi.
   - **JLabel** (2 cái): Gắn nhãn `Tìm theo tên:` và `Tìm theo tuổi:`.
   - **JButton** (5 cái): `Thêm`, `Sửa`, `Xóa`, `Tìm kiếm`, `Sắp xếp`.
   - **JLabel** (1 cái): Hiển thị thống kê (tổng nhân viên, tổng lương).
4. Sắp xếp giao diện (dùng Absolute Layout):
   ```
   [JTable]
   Tìm theo tên: [Text Field]  Tìm theo tuổi: [Text Field]
   [Thêm] [Sửa] [Xóa] [Tìm kiếm] [Sắp xếp]
   Thống kê: [Label]
   ```
5. Đặt tên biến trong `Properties`:
   - JTable: `employeeTable`
   - JTextField (tên): `searchNameField`
   - JTextField (tuổi): `searchAgeField`
   - JButton: `addButton`, `updateButton`, `deleteButton`, `searchButton`, `sortButton`
   - JLabel (thống kê): `statsLabel`
6. Cài đặt form:
   - Đặt `title` trong `Properties`: `Quản lý nhân sự`.
   - Thêm `setLocationRelativeTo(null)` trong `Custom Creation Code`.
7. Thêm sự kiện:
   - Double-click mỗi nút (`Thêm`, `Sửa`, `Xóa`, `Tìm kiếm`, `Sắp xếp`) để tạo các phương thức `ActionPerformed`.
8. Lưu form.

#### **Bước 13: Tạo `AddEmployeeView`**
1. Right-click lên package `com.mycompany.qlns.view` > `New > JFrame Form`.
2. Đặt tên: `AddEmployeeView` > Nhấn `Finish`.
3. Thêm thành phần:
   - **JLabel** (5 cái): `ID:`, `Tên:`, `Ngày sinh (yyyy-MM-dd):`, `Giới tính:`, `Vai trò:`, `Lương:`.
   - **JTextField** (4 cái): Cho ID, tên, ngày sinh, vai trò, lương.
   - **JComboBox**: Cho giới tính (Nam/Nữ).
   - **JButton**: `Lưu`.
4. Sắp xếp (Absolute Layout):
   ```
   ID: [Text Field]
   Tên: [Text Field]
   Ngày sinh: [Text Field]
   Giới tính: [ComboBox]
   Vai trò: [Text Field]
   Lương: [Text Field]
   [Lưu]
   ```
5. Đặt tên biến:
   - JTextField: `idField`, `nameField`, `birthDateField`, `roleField`, `salaryField`
   - JComboBox: `genderCombo`
   - JButton: `saveButton`
6. Cài đặt:
   - Đặt `title`: `Thêm/Sửa nhân viên`.
   - Thêm `setLocationRelativeTo(null)`.
7. Double-click nút `Lưu` để tạo phương thức `saveButtonActionPerformed`.
8. Lưu form.

#### **Bước 14: Tạo `EmployeeDao`**
1. Right-click lên package `com.mycompany.qlns.dao` > `New > Java Class`.
2. Đặt tên: `EmployeeDao` > Nhấn `Finish`.
3. Copy code:

```java
package com.mycompany.qlns.dao;

import com.mycompany.qlns.model.Employee;
import com.mycompany.qlns.utils.FileUtils;
import java.util.ArrayList;
import java.util.List;

public class EmployeeDao {
    private List<Employee> employees;
    private String filePath = "XML/employees.xml";

    public EmployeeDao() {
        employees = new ArrayList<>();
        loadEmployees();
    }

    public void loadEmployees() {
        employees = FileUtils.readFromXML(filePath);
        if (employees == null) {
            employees = new ArrayList<>();
        }
        int maxId = 0;
        for (Employee emp : employees) {
            if (emp.getId() > maxId) {
                maxId = emp.getId();
            }
        }
        Employee.setNextId(maxId + 1);
    }

    public void saveEmployees() {
        FileUtils.writeToXML(employees, filePath);
    }

    public List<Employee> getEmployees() {
        return employees;
    }
}
```

4. Lưu file.

#### **Bước 15: Tạo `FileUtils`**
1. Right-click lên package `com.mycompany.qlns.utils` > `New > Java Class`.
2. Đặt tên: `FileUtils` > Nhấn `Finish`.
3. Copy code:

```java
package com.mycompany.qlns.utils;

import com.mycompany.qlns.model.Employee;
import java.io.File;
import java.util.ArrayList;
import java.util.List;
import javax.xml.bind.JAXBContext;
import javax.xml.bind.Marshaller;
import javax.xml.bind.Unmarshaller;
import javax.xml.bind.annotation.XmlElement;
import javax.xml.bind.annotation.XmlRootElement;

@XmlRootElement(name = "employees")
public class FileUtils {
    private List<Employee> employees;

    public FileUtils() {
        employees = new ArrayList<>();
    }

    @XmlElement(name = "employee")
    public List<Employee> getEmployees() {
        return employees;
    }

    public void setEmployees(List<Employee> employees) {
        this.employees = employees;
    }

    public static void writeToXML(List<Employee> employees, String filePath) {
        try {
            FileUtils wrapper = new FileUtils();
            wrapper.setEmployees(employees);
            JAXBContext context = JAXBContext.newInstance(FileUtils.class);
            Marshaller marshaller = context.createMarshaller();
            marshaller.setProperty(Marshaller.JAXB_FORMATTED_OUTPUT, true);
            marshaller.marshal(wrapper, new File(filePath));
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    public static List<Employee> readFromXML(String filePath) {
        try {
            File file = new File(filePath);
            if (!file.exists()) {
                return new ArrayList<>();
            }
            JAXBContext context = JAXBContext.newInstance(FileUtils.class);
            Unmarshaller unmarshaller = context.createUnmarshaller();
            FileUtils wrapper = (FileUtils) unmarshaller.unmarshal(file);
            return wrapper.getEmployees();
        } catch (Exception e) {
            e.printStackTrace();
            return new ArrayList<>();
        }
    }
}
```

4. Lưu file.

#### **Bước 16: Tạo `EmployeeController`**
1. Right-click lên package `com.mycompany.qlns.controller` > `New > Java Class`.
2. Đặt tên: `EmployeeController` > Nhấn `Finish`.
3. Copy code:

```java
package com.mycompany.qlns.controller;

import com.mycompany.qlns.dao.EmployeeDao;
import com.mycompany.qlns.model.Employee;
import com.mycompany.qlns.view.EmployeeView;
import java.time.LocalDate;
import java.util.ArrayList;
import java.util.Comparator;
import java.util.List;
import java.util.stream.Collectors;

public class EmployeeController {
    private EmployeeDao dao;
    private EmployeeView view;

    public EmployeeController() {
        dao = new EmployeeDao();
    }

    public void setView(EmployeeView view) {
        this.view = view;
        loadEmployees();
        updateView();
    }

    public void loadEmployees() {
        dao.loadEmployees();
    }

    public boolean addEmployee(Employee emp) {
        for (Employee e : dao.getEmployees()) {
            if (e.getId() == emp.getId() || e.getName().equalsIgnoreCase(emp.getName())) {
                return false;
            }
        }
        dao.getEmployees().add(emp);
        dao.saveEmployees();
        updateView();
        return true;
    }

    public boolean updateEmployee(int id, Employee emp) {
        for (int i = 0; i < dao.getEmployees().size(); i++) {
            if (dao.getEmployees().get(i).getId() == id) {
                dao.getEmployees().set(i, emp);
                dao.saveEmployees();
                updateView();
                return true;
            }
        }
        return false;
    }

    public boolean deleteEmployee(int id) {
        for (int i = 0; i < dao.getEmployees().size(); i++) {
            if (dao.getEmployees().get(i).getId() == id) {
                dao.getEmployees().remove(i);
                dao.saveEmployees();
                updateView();
                return true;
            }
        }
        return false;
    }

    public List<Employee> searchByName(String name) {
        List<Employee> result = dao.getEmployees().stream()
                .filter(e -> e.getName().toLowerCase().contains(name.toLowerCase()))
                .collect(Collectors.toList());
        return result;
    }

    public List<Employee> searchByAge(int age) {
        List<Employee> result = dao.getEmployees().stream()
                .filter(e -> e.getAge() == age)
                .collect(Collectors.toList());
        return result;
    }

    public List<Employee> sortByName() {
        List<Employee> sorted = new ArrayList<>(dao.getEmployees());
        sorted.sort(Comparator.comparing(Employee::getName));
        return sorted;
    }

    public int getTotalEmployees() {
        return dao.getEmployees().size();
    }

    public double getTotalSalary() {
        return dao.getEmployees().stream().mapToDouble(Employee::getSalary).sum();
    }

    public void updateView() {
        view.updateTable(dao.getEmployees());
        view.updateStats(getTotalEmployees(), getTotalSalary());
    }
}
```

4. Lưu file.

#### **Bước 17: Cập nhật `EmployeeView`**
1. Mở `EmployeeView.java` trong tab Source.
2. Thêm thuộc tính, constructor, và các phương thức:

```java
package com.mycompany.qlns.view;

import com.mycompany.qlns.controller.EmployeeController;
import com.mycompany.qlns.model.Employee;
import java.util.List;
import javax.swing.table.DefaultTableModel;

public class EmployeeView extends javax.swing.JFrame {
    private EmployeeController controller;
    private DefaultTableModel tableModel;

    public EmployeeView(EmployeeController controller) {
        this.controller = controller;
        initComponents();
        setLocationRelativeTo(null);
        tableModel = new DefaultTableModel(new Object[]{"ID", "Tên", "Tuổi", "Giới tính", "Vai trò", "Lương"}, 0);
        employeeTable.setModel(tableModel);
    }

    public void updateTable(List<Employee> employees) {
        tableModel.setRowCount(0);
        for (Employee emp : employees) {
            tableModel.addRow(new Object[]{emp.getId(), emp.getName(), emp.getAge(), emp.getGender(), emp.getRole(), emp.getSalary()});
        }
    }

    public void updateStats(int totalEmployees, double totalSalary) {
        statsLabel.setText("Tổng nhân viên: " + totalEmployees + " | Tổng lương: " + totalSalary);
    }

    private void initComponents() {
        // (Code do NetBeans sinh, giữ nguyên)
    }

    private void addButtonActionPerformed(java.awt.event.ActionEvent evt) {
        AddEmployeeView addView = new AddEmployeeView(controller, null);
        addView.setVisible(true);
    }

    private void updateButtonActionPerformed(java.awt.event.ActionEvent evt) {
        int selectedRow = employeeTable.getSelectedRow();
        if (selectedRow >= 0) {
            int id = (int) tableModel.getValueAt(selectedRow, 0);
            Employee emp = controller.getEmployees().stream()
                    .filter(e -> e.getId() == id)
                    .findFirst()
                    .orElse(null);
            AddEmployeeView addView = new AddEmployeeView(controller, emp);
            addView.setVisible(true);
        } else {
            javax.swing.JOptionPane.showMessageDialog(this, "Chọn nhân viên để sửa!", "Lỗi", javax.swing.JOptionPane.ERROR_MESSAGE);
        }
    }

    private void deleteButtonActionPerformed(java.awt.event.ActionEvent evt) {
        int selectedRow = employeeTable.getSelectedRow();
        if (selectedRow >= 0) {
            int id = (int) tableModel.getValueAt(selectedRow, 0);
            if (controller.deleteEmployee(id)) {
                javax.swing.JOptionPane.showMessageDialog(this, "Xóa thành công!");
            } else {
                javax.swing.JOptionPane.showMessageDialog(this, "Xóa thất bại!", "Lỗi", javax.swing.JOptionPane.ERROR_MESSAGE);
            }
        } else {
            javax.swing.JOptionPane.showMessageDialog(this, "Chọn nhân viên để xóa!", "Lỗi", javax.swing.JOptionPane.ERROR_MESSAGE);
        }
    }

    private void searchButtonActionPerformed(java.awt.event.ActionEvent evt) {
        String name = searchNameField.getText().trim();
        String ageStr = searchAgeField.getText().trim();
        List<Employee> result = controller.getEmployees();
        if (!name.isEmpty()) {
            result = controller.searchByName(name);
        }
        if (!ageStr.isEmpty()) {
            try {
                int age = Integer.parseInt(ageStr);
                result = controller.searchByAge(age);
            } catch (NumberFormatException e) {
                javax.swing.JOptionPane.showMessageDialog(this, "Tuổi phải là số!", "Lỗi", javax.swing.JOptionPane.ERROR_MESSAGE);
            }
        }
        updateTable(result);
    }

    private void sortButtonActionPerformed(java.awt.event.ActionEvent evt) {
        updateTable(controller.sortByName());
    }
}
```

3. Sửa `initComponents` nếu cần (dùng tab Design để đảm bảo các thành phần khớp).
4. Thêm import:

```java
import com.mycompany.qlns.controller.EmployeeController;
import com.mycompany.qlns.model.Employee;
import java.util.List;
import javax.swing.table.DefaultTableModel;
```

5. Lưu file.

#### **Bước 18: Cập nhật `AddEmployeeView`**
1. Mở `AddEmployeeView.java` trong tab Source.
2. Thêm code:

```java
package com.mycompany.qlns.view;

import com.mycompany.qlns.controller.EmployeeController;
import com.mycompany.qlns.model.Employee;
import java.time.LocalDate;
import java.time.format.DateTimeFormatter;

public class AddEmployeeView extends javax.swing.JFrame {
    private EmployeeController controller;
    private Employee employee;

    public AddEmployeeView(EmployeeController controller, Employee employee) {
        this.controller = controller;
        this.employee = employee;
        initComponents();
        setLocationRelativeTo(null);
        genderCombo.addItem("Nam");
        genderCombo.addItem("Nữ");
        if (employee != null) {
            idField.setText(String.valueOf(employee.getId()));
            idField.setEditable(false);
            nameField.setText(employee.getName());
            birthDateField.setText(employee.getBirthDate().format(DateTimeFormatter.ofPattern("yyyy-MM-dd")));
            genderCombo.setSelectedItem(employee.getGender());
            roleField.setText(employee.getRole());
            salaryField.setText(String.valueOf(employee.getSalary()));
        }
    }

    private void initComponents() {
        // (Code do NetBeans sinh, giữ nguyên)
    }

    private void saveButtonActionPerformed(java.awt.event.ActionEvent evt) {
        try {
            int id = employee == null ? Employee.getNextId() : employee.getId();
            String name = nameField.getText().trim();
            if (name.isEmpty()) {
                throw new IllegalArgumentException("Tên không được rỗng!");
            }
            LocalDate birthDate = LocalDate.parse(birthDateField.getText(), DateTimeFormatter.ofPattern("yyyy-MM-dd"));
            String gender = (String) genderCombo.getSelectedItem();
            String role = roleField.getText().trim();
            double salary = Double.parseDouble(salaryField.getText());
            if (salary < 0) {
                throw new IllegalArgumentException("Lương không được âm!");
            }
            Employee emp = new Employee(id, name, birthDate, gender, role, salary);
            boolean success = employee == null ? controller.addEmployee(emp) : controller.updateEmployee(id, emp);
            if (success) {
                javax.swing.JOptionPane.showMessageDialog(this, "Lưu thành công!");
                dispose();
            } else {
                javax.swing.JOptionPane.showMessageDialog(this, "Trùng ID hoặc tên!", "Lỗi", javax.swing.JOptionPane.ERROR_MESSAGE);
            }
        } catch (Exception e) {
            javax.swing.JOptionPane.showMessageDialog(this, "Lỗi: " + e.getMessage(), "Lỗi", javax.swing.JOptionPane.ERROR_MESSAGE);
        }
    }
}
```

3. Thêm import:

```java
import com.mycompany.qlns.controller.EmployeeController;
import com.mycompany.qlns.model.Employee;
import java.time.LocalDate;
import java.time.format.DateTimeFormatter;
```

4. Lưu file.

---

### **Tuần 5-6: Tích hợp và hoàn thiện GUI**

#### **Bước 19: Cập nhật `LoginController`**
1. Mở `LoginController.java`, đảm bảo code khớp với bước 9 (đã có `showMainView` để mở `EmployeeView`).

#### **Bước 20: Test toàn bộ**
1. Nhấn `F6` để chạy dự án.
2. Thử các chức năng:
   - Đăng nhập với `admin`/`123`.
   - Thêm nhân viên qua nút `Thêm`.
   - Sửa/xóa nhân viên từ `JTable`.
   - Tìm kiếm theo tên/tuổi.
   - Sắp xếp theo tên.
   - Kiểm tra thống kê trên `statsLabel`.
3. Kiểm tra file `XML/employees.xml` trong thư mục `XML` để đảm bảo dữ liệu lưu đúng.

#### **Bước 21: Xử lý lỗi**
1. Trong `FileUtils`, thêm `JOptionPane` cho lỗi XML:

```java
public static void writeToXML(List<Employee> employees, String filePath) {
    try {
        // (Giữ nguyên code)
    } catch (Exception e) {
        javax.swing.JOptionPane.showMessageDialog(null, "Lỗi lưu file XML: " + e.getMessage(), "Lỗi", javax.swing.JOptionPane.ERROR_MESSAGE);
    }
}

public static List<Employee> readFromXML(String filePath) {
    try {
        // (Giữ nguyên code)
    } catch (Exception e) {
        javax.swing.JOptionPane.showMessageDialog(null, "Lỗi đọc file XML: " + e.getMessage(), "Lỗi", javax.swing.JOptionPane.ERROR_MESSAGE);
        return new ArrayList<>();
    }
}
```

2. Lưu file.

---

### **Tuần 7-8: Kiểm tra và hoàn thiện**

#### **Bước 22: Tạo lớp test**
1. Right-click lên package `com.mycompany.qlns` > `New > Java Class`.
2. Đặt tên: `TestEmployee` > Nhấn `Finish`.
3. Copy code:

```java
package com.mycompany.qlns;

import com.mycompany.qlns.controller.EmployeeController;
import com.mycompany.qlns.model.Employee;
import java.time.LocalDate;
import java.util.List;

public class TestEmployee {
    public static void main(String[] args) {
        EmployeeController controller = new EmployeeController();

        // Test thêm
        Employee emp1 = new Employee(Employee.getNextId(), "Nguyen Van A", LocalDate.of(1995, 5, 20), "Nam", "Nhan vien", 10000000);
        Employee emp2 = new Employee(Employee.getNextId(), "Tran Thi B", LocalDate.of(1990, 3, 15), "Nữ", "Quản lý", 15000000);
        System.out.println("Thêm emp1: " + controller.addEmployee(emp1));
        System.out.println("Thêm emp2: " + controller.addEmployee(emp2));

        // Test tìm kiếm
        List<Employee> byName = controller.searchByName("Nguyen");
        System.out.println("Tìm theo tên 'Nguyen': " + byName);

        // Test thống kê
        System.out.println("Tổng nhân viên: " + controller.getTotalEmployees());
        System.out.println("Tổng lương: " + controller.getTotalSalary());
    }
}
```

4. Right-click file `TestEmployee.java` > `Run File` (Shift + F6).
5. Kiểm tra output trong tab `Output`.

#### **Bước 23: Hoàn thiện**
1. Dùng `Source > Format` trong NetBeans để định dạng code.
2. Thêm comment:
   - Right-click trong code > `Insert Code > Add Javadoc` cho mỗi phương thức.
3. Right-click dự án > `Clean and Build` để kiểm tra lỗi.
4. Test lại toàn bộ GUI và logic.

---

### **Lưu ý cho sinh viên**
- **NetBeans GUI Builder**: Dùng tab Design để kéo thả, tab Source để chỉnh code.
- **Debug**: Đặt breakpoint (click trái lề dòng code), nhấn `Ctrl + F5` để debug.
- **Lưu thường xuyên**: Nhấn `Ctrl + S` sau mỗi bước.
- **Kiểm tra XML**: Mở `XML/employees.xml` trong tab `Files` để xem dữ liệu.
- **Nếu gặp lỗi**:
  - Kiểm tra import (NetBeans gợi ý bằng `Alt + Enter`).
  - Đảm bảo JAR JAXB có trong `Libraries`.

---

### **Kết quả**
Sau 8 tuần, sinh viên sẽ có:
- Dự án `QLNS` với cấu trúc rõ ràng.
- GUI hoàn chỉnh: Đăng nhập, danh sách nhân viên, form thêm/sửa.
- Chức năng đầy đủ: Đăng nhập, CRUD, tìm kiếm, thống kê, lưu XML.
- Code dễ hiểu, có thể test qua console (`TestEmployee`) hoặc GUI.

Nếu cần thêm code mẫu (ví dụ: `EmployeeView` đầy đủ) hoặc hướng dẫn chi tiết hơn cho một bước, hãy yêu cầu!