# -Chatgpt-Project-BookContextAI


# Chatgpt-Project-BookContextAI

- 1. 데이터 수집 및 전처리
    - 알라딘 데이터 수집 : 30개
    - 컬럼 : 제목, 저자, 목차, 책소개, 구매 링크, 가격, 장르, 책속에서(책 페이지 문구), 편집장의 선택
    - 장르(주제 분류) : 무조건 첫번째 줄의 세번째까지만 수집
    - 한명당 한 섹터씩 → 10년치 (2013-08-17 ~ 2023-08-27) / 너무 버겁다 : 5년치
        - 사회과학
        
    - 1) 알라딘 크롤링 이슈 (😤)
        1. beautifulsoup requests 안됨 → 동적으로 생성된 정보 로드 못함 → 일정 시간 후나 특정 사용자 액션에 의해 로드되는 콘텐츠의 경우 Selenium과 같은 도구를 사용해야 합
        2. ****selenium 사용 안됨****
            
            ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/6d6a8552-b3a8-4ae8-9f74-fc18491512af/Untitled.png)
            
            - 1, 2 방법 해봤지만 안됨
            - 결론: 웹피이지 접근 제한 인듯 (아오,,)
            
    
    - 2) 교보문교 크롤링 (성공)
        - 교보문교 사이트로 크롤링 시도
        
        1. ****selenium 라이브러리 (성공)****
        
        - 목차 크롤링 코드
            
            ```jsx
            from selenium.webdriver import Chrome, ChromeOptions
            from selenium.webdriver.common.by import By
            from selenium.webdriver.support.ui import WebDriverWait
            from selenium.webdriver.support import expected_conditions as EC
            
            op = ChromeOptions()
            op.add_argument('--headless')
            op.add_argument('--no-sandbox')
            op.add_argument('--disable-dev-shm-usage')
            
            driver = Chrome(options=op)
            
            url = "https://product.kyobobook.co.kr/detail/S000202671445"
            driver.get(url)
            
            # 웹 페이지 로딩을 기다립니다.
            wait = WebDriverWait(driver, 20)
            css_selector = "li.book_contents_item"
            data_element = wait.until(EC.presence_of_element_located((By.CSS_SELECTOR, css_selector)))
            
            print(data_element.text)
            
            # 브라우저를 종료합니다.
            driver.quit()
            ```
            
            (실행결과)
            
            ```jsx
            서문-과학 공부의 즐거움
            
            1. 그럴법한 이야기와 확실한 진리 (인문학과 과학)
            거만한 바보 ㆍ 운명적 문과의 슬픔 ㆍ 인문학과 과학의 비대칭 ㆍ 우리 집과 우리 엄마의 진실
            
            2. 나는 무엇인가 (뇌과학)
            내가 누구인지 말할 수 있는 자는 누구인가 ㆍ 1.4킬로그램의 우주 ㆍ 신경세포와 경제법칙 ㆍ 현상과 사물 자체 ㆍ 칸트 철학과 양자역학 ㆍ 측은지심과 거울신경세포 ㆍ 자유의지
            
            3. 우리는 왜 존재하는가 (생물학)
            좌파, 우파, 다윈주의 ㆍ 생명의 알파벳 ㆍ 유전자와 인생론 ㆍ 생물학 패권주의 ㆍ 사회생물학과 사회주의 ㆍ 이타 행동의 비밀
            
            4. 단순한 것으로 복잡한 것을 설명할 수 있는가 (화학)
            화학은 억울하다 ㆍ 위대한 전자 ㆍ 주기율표 ㆍ 탄소, 유능한 중도 ㆍ 환원주의 논쟁 ㆍ 통섭의 어려움
            
            5. 우리는 어디서 왔고 어디로 가는가 (물리학)
            불확정성 원리 ㆍ 상대성이론 ㆍ 별에서 온 그대 ㆍ 양자역학, 불교, 유물변증법 ㆍ 엔트로피 묵시록
            
            6. 우주의 언어인가 천재들의 놀이인가 (수학)
            수학의 아름다움 ㆍ 천재들의 지적 유희 ㆍ 난 부럽지가 않아
            
            후기-바보를 겨우 면한 자의 무모한 도전
            찾아보기
            ```
            
        
        1. beautifulsoup requests 라이브러리 (??)
        - 목차 크롤링 코드
            
            ```jsx
            from selenium.webdriver import Chrome, ChromeOptions
            from selenium.webdriver.common.by import By
            from selenium.webdriver.support.ui import WebDriverWait
            from selenium.webdriver.support import expected_conditions as EC
            
            op = ChromeOptions()
            op.add_argument('--headless')
            op.add_argument('--no-sandbox')
            op.add_argument('--disable-dev-shm-usage')
            
            driver = Chrome(options=op)
            
            url = "https://product.kyobobook.co.kr/detail/S000202671445"
            driver.get(url)
            
            # 웹 페이지 로딩을 기다립니다.
            wait = WebDriverWait(driver, 20)
            css_selector = "li.book_contents_item"
            data_element = wait.until(EC.presence_of_element_located((By.CSS_SELECTOR, css_selector)))
            
            print(data_element.text)
            
            # 브라우저를 종료합니다.
            driver.quit()
            ```
            
        - 교보문교 사이트 맞춤 컬럼 재정의:
            - 제목, 저자, 편집장의 선택, 책소개, 목차, 가격, 평점, 구매링크, 장르1, 장르2
                - 여기서 장르1, 장르2 나눈 이유는 다음과 같다.
                    
                    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/87d0a6b0-d599-446c-abff-f38f277f78fe/Untitled.png)
                    
                    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/122c00d3-870d-4ceb-8dfa-fff99abaeffe/Untitled.png)
                    
                - 두 줄 다 맞는 말이다.
                - 하지만 3개 이상은 가감히 자른다
                    
                    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/9eb2d9d7-0cab-4eae-8922-36cd15969c22/Untitled.png)
                    
        - (실행결과)
            
            정상적으로 저장 잘 되었다.. (결론: 교보문교 짱)
            
            ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/0353c4a4-95d1-4208-a841-378eaed6f690/Untitled.png)
            
            ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/7e53c0f3-04b8-475b-aff3-a894394ea932/Untitled.png)
            
    
    - 3) 교보문교 장르, 컬럼 재정의
        - 장르: 국내도서 장르: 29개
        - It’s me, 사회과학 is none ⇒ 정치/사회 or 역사/문화 select
        
        ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b0bc8264-e6a3-4b96-b3f5-58ae3db56f58/Untitled.png)
        
        - 컬럼: 1000 rows × 20 columns DATA 생성
        - 이미지 파일명은 이미지 다운 잘되었는지 리스트 확인 변수로 제거 예정
        
        ```
        # 크롤링한 정보와 추가 정보를 결합
            book_data = {
                "제목": title,
                "저자": author,
                "출판사": additional_info["출판사"],
                "편집장의 선택": editor_choice,
                "책소개": book_intro,
                "목차": contents,
                "주간베스트": best_link_text,
                "평점": rating,
                "구매자 응답": good_response,
                "구매리뷰": combined_comments,
                "구매링크": purchase_link,
                "장르1": genre1,
                "장르2": genre2,
                "정가": additional_info["정가"],
                "판매가": price,
                "할인율": additional_info["할인율"],
                "적립율": additional_info["적립율"],
                "적립예정포인트": additional_info["적립예정포인트"],
                "출판사": additional_info["출판사"],
                "발행(출시)일자": reformat_date(additional_info["발행(출시)일자"]),
                "이미지 파일명": cleaned_image_filename,
            }
        ```
        
        [](https://www.kyobobook.co.kr/)
        
        
        - 4) 교보문교 크롤링 (카테고리 성공)
        
        최종 코드: [https://github.com/seunghoonss/Chatgpt-Project-BookContextAI/blob/main/Crawling Data/교보문교_크롤링(성공).ipynb](https://github.com/seunghoonss/Chatgpt-Project-BookContextAI/blob/main/Crawling%20Data/%EA%B5%90%EB%B3%B4%EB%AC%B8%EA%B5%90_%ED%81%AC%EB%A1%A4%EB%A7%81(%EC%84%B1%EA%B3%B5).ipynb)
        
        - 카테고리(정치/사회) 베스트셀러 접속, Excel 파일 다운로드
        - 판매 상품 ID 컬럼 데이터 활용하여 전체 도서 url 데이터 가공
            
            ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/620605ec-ae35-4a5f-ae4c-52002aa28351/Untitled.png)
            
        - 위의 데이터에서 활용할 수 있는 컬럼 선정: 판매상품 ID, 정가, 할인율, 적립율, 적립예정포인트, 출판사, 발생(출시)일자: 총 8(-1)개
            
            
        - 코드 리뷰 (로직)
            1. 라이브러리 import
            2. ChromeOptoins 설정: 롬 드라이버의 옵션을 헤드리스 모드로 설정하여 GUI 없이 실행
            3. 이미지 저장 함수: save_image_from_url 함수를 이용하여 이미지 URL에서 이미지를 다운로드하고 지정된 경로에 저장하도록 함
            4. 파일명 정제 함수: 파일명에 사용할 수 없는 특수 문자나 공백을 제거하거나 대체하는 역할
            5. 데이터 추출 함수: 웹 페이지의 특정 요소에서 데이터를 추출하는 기능을 하는 get_element_data 함수가 정의되어 있다. 이 함수는 주어진 CSS 선택자로 웹 페이지의 요소를 선택하고, 해당 요소의 텍스트나 속성 값을 반환
            6. 날짜 변환 함수: YYYYMMDD 형식의 날짜를 보기 좋게 변환하는 역할
            7. 크롤링 함수: crawl_kyobobook 함수는 주어진 아이템 ID와 추가 정보를 기반으로 교보문고 웹사이트에서 책 정보를 크롤링
            8. 메인 크롤링 루프: 이 부분에서는 데이터프레임에 있는 각 행에 대해 크롤링을 수행하며, 크롤링 결과를 리스트에 추가
            9. 결과 저장: 크롤링 결과를 CSV 파일로 저장
        
        - 실행 결과 (성공)
            
            ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/29aacb36-bab2-4527-9266-9db80028f0a6/Untitled.png)
            
            - 1000개 데이터 성공적으로 Load
            
            ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/53f04951-3548-4db6-b67f-250ec07aeb88/Untitled.png)
            
            - text데이터 잘 저장됨
                
                [](https://drive.google.com/file/d/1qwBnDQjxFOmcRBuJYwhfnBsOjb-Og2mC/view?usp=drive_link)
                
        
        ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/364a616f-bcee-45ed-953c-87542b699a57/Untitled.png)
        
        - 이미지 데이터도 잘 저장됨
