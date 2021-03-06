이 섹션에서는 새 영업 그룹과 함께 디렉터리에 새 사용자를 두 명 추가합니다. 사용자 중 한 명에는 영업 그룹에 대한 멤버 자격이 부여됩니다. 다른 사용자에게는 그룹에 대한 멤버 자격이 부여되지 않습니다.

### 사용자 만들기


1. [Azure 클래식 포털](https://manage.windowsazure.com)에서, 자습서에 따라 인증을 앱에 추가할 때 이전에 인증을 위해 구성된 디렉터리로 이동합니다.
2. 페이지 위쪽에서 **사용자**를 클릭합니다. 그런 다음 아래쪽에 있는 **사용자 추가** 단추를 클릭합니다. 
3. **Bob**이라는 이름의 사용자를 만들어 새 사용자 대화 상자를 완료합니다. 사용자에 대한 임시 암호를 기록합니다. 
4. **Dave**라는 이름의 다른 사용자를 만듭니다. 사용자에 대한 임시 암호를 기록합니다.
5. 새 사용자는 아래와 유사하게 나타납니다.

    ![](./media/mobile-services-aad-rbac-create-sales-group/users.png)


### 영업 그룹 만들기


1. 디렉터리 페이지 맨 위에 있는 **그룹**을 클릭합니다. 그런 다음 아래쪽에 있는 **그룹 추가** 단추를 클릭합니다. 
2. 그룹의 이름으로 **영업**을 입력하고 대화 상자의 완료 단추를 눌러 그룹을 만듭니다. 

    ![](./media/mobile-services-aad-rbac-create-sales-group/sales-group.png)

### 영업 그룹에 사용자 멤버 자격 추가


1. 디렉터리 페이지 위쪽에서 **그룹**을 클릭합니다. 그런 다음 **영업** 그룹을 클릭하여 영업 그룹 페이지로 이동합니다. 
2. 영업 그룹 페이지에서 **멤버 자격 추가**를 클릭합니다. 영업 그룹에 **Bob** 이름의 사용자를 추가합니다. **Dave** 이름의 사용자는 그룹의 구성원이 아닙니다.

    ![](./media/mobile-services-aad-rbac-create-sales-group/group-membership.png)

3. 영업 그룹 페이지에서 **속성**을 클릭한 다음 페이지의 맨 아래에서 영업 그룹의 **개체 ID**를 복사합니다.

   
    ![](./media/mobile-services-aad-rbac-create-sales-group/sales-group-id.png)

4. 모바일 서비스 구성 페이지로 다시 이동하고 **AAD\_SALES\_GROUP\_ID**라는 앱 설정으로 개체 ID를 추가합니다. 이 자습서에서는 그룹 이름을 기반으로 ID를 확인하지 않고 앱 설정으로 그룹의 개체 ID를 사용 합니다. 그룹 이름이 변경될 수도 있지만 ID는 동일하게 유지되기 때문입니다.

    ![](./media/mobile-services-aad-rbac-create-sales-group/sales-group-id-app-setting.png)

<!---HONumber=AcomDC_1203_2015-->