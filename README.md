#include <stdio.h>
#include <stdlib.h>
#include <string.h>

// 큐시트 한 줄(한 순서)을 저장할 구조체 정의
typedef struct {
    char title[50];    // 순서명
    char cast[30];     // 출연자/담당자
    char remark[100];  // 비고 (조명, 음향 등)
    int start_h, start_m; // 시작 시, 분
    int end_h, end_m;     // 종료 시, 분
    int duration;         // 소요 시간(분)
} CueSheetItem;


void clearBuffer() {
    while (getchar() != '\n');
}

int main() {
    CueSheetItem items[20]; // 최대 20개 순서 저장 가능
    int itemCount = 0;      // 현재 저장된 순서 개수

    int start_hour, start_min, end_hour, end_min;
    int total_limit_min, current_time_min;
    int remain_min;

    char choice;

    printf("=== 대학 축제/행사 큐시트 매니저 ===\n\n");

    // 1. 전체 행사 시간 설정
    printf("행사 시작 시간 (예: 18:00 ): ");
    if (scanf("%d:%d", &start_hour, &start_min) != 2) {
        scanf("%d %d", &start_hour, &start_min);
    }
    clearBuffer();

    printf("행사 종료 예정 시간 (예: 21:00 ): ");
    if (scanf("%d:%d", &end_hour, &end_min) != 2) {
        scanf("%d %d", &end_hour, &end_min);
    }
    clearBuffer();

    // 전체 시간 계산 (분 단위)
    int start_total = start_hour * 60 + start_min;
    int end_total = end_hour * 60 + end_min;
    total_limit_min = end_total - start_total;
    current_time_min = start_total; // 현재 시간 커서는 시작 시간으로 초기화
    remain_min = total_limit_min;

    printf("\n>> 총 가용 시간: %d분\n", total_limit_min);
    printf(">> 이제 순서를 하나씩 추가합니다. (이전 순서 종료 시간이 다음 순서 시작 시간이 됩니다)\n");

    // 2. 순서 입력 반복문
    while (remain_min > 0 && itemCount < 20) {
        printf("\n----------------------------------------\n");
        printf("[ 순서 %d 입력 ] (남은 시간: %d분)\n", itemCount + 1, remain_min);

        // 구조체에 정보 입력 받기
        printf("1. 순서명 (예: 개회식): ");
        fgets(items[itemCount].title, sizeof(items[itemCount].title), stdin);
        items[itemCount].title[strcspn(items[itemCount].title, "\n")] = 0; // 줄바꿈 제거

        printf("2. 출연/담당 (예: 총학생회장): ");
        fgets(items[itemCount].cast, sizeof(items[itemCount].cast), stdin);
        items[itemCount].cast[strcspn(items[itemCount].cast, "\n")] = 0; // 줄바꿈 제거

        printf("3. 비고 (예: BGM 1번, 마이크 ON): ");
        fgets(items[itemCount].remark, sizeof(items[itemCount].remark), stdin);
        items[itemCount].remark[strcspn(items[itemCount].remark, "\n")] = 0; // 줄바꿈 제거

        printf("4. 소요 시간(분) 입력: ");
        scanf("%d", &items[itemCount].duration);
        clearBuffer();

        // 시간 계산  저장
        items[itemCount].start_h = current_time_min / 60;
        items[itemCount].start_m = current_time_min % 60;

        current_time_min += items[itemCount].duration; // 시간 반영

        items[itemCount].end_h = current_time_min / 60;
        items[itemCount].end_m = current_time_min % 60;

        remain_min -= items[itemCount].duration;
        itemCount++;

        // 입력 결과 보여주기
        printf(">> [등록 완료] %02d:%02d ~ %02d:%02d (%s)\n",
            items[itemCount - 1].start_h, items[itemCount - 1].start_m,
            items[itemCount - 1].end_h, items[itemCount - 1].end_m,
            items[itemCount - 1].title);

        if (remain_min <= 0) {
            printf("\n⚠️ 시간이 모두 소진되거나 초과되었습니다!\n");
            break;
        }

        printf(">> 계속 추가하시겠습니까? (y/n): ");
        scanf("%c", &choice);
        clearBuffer();
        if (choice == 'n' || choice == 'N') break;
    }

    // 3. 최종 큐시트 출력 
    printf("\n\n================ [ 최종 큐시트 ] ================\n");
    printf("%-4s , %-13s , %-20s , %-15s , %-20s\n", "순서", "시  간", "내  용", "출  연", "비  고");
   

    for (int i = 0; i < itemCount; i++) {
        printf("%-4d , %02d:%02d ~ %02d:%02d , %-20s , %-15s  ,%-20s\n",
            i + 1,
            items[i].start_h, items[i].start_m,
            items[i].end_h, items[i].end_m,
            items[i].title, items[i].cast, items[i].remark);
    }
    printf("==============================================================================\n");
    printf("\n작성이 완료되었습니다. 프로그램을 종료합니다.\n");

    return 0;
}
