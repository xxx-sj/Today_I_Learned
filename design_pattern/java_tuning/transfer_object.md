# transfer_object

```
일일이 null check를 하지않아도 되는 이점이 있다.
```

```java

class EmployeeTO implements Serializable {
    private String empName;
    private String empID;
    private String empPhone;

    public String getEmpName() {
        if(empName == null) return "";
        return empName;
    }

    public void setEmpName(String empName) {
        this.empName = empName;
    }

    public String getEmpID() {
        return empID;
    }

    public void setEmpID(String empID) {
        this.empID = empID;
    }

    public String getEmpPhone() {
        return empPhone;
    }

    public void setEmpPhone(String empPhone) {
        this.empPhone = empPhone;
    }

    @Override
    public String toString() {
        StringBuilder sb = new StringBuilder();
        sb.append("empName").append(empName).append(" empID=").append(empID).append(" empPhone=").append(empPhone);

        return sb.toString();
    }
}
```


##### 출처: 자바성능 튜닝 이야기
