# 영문 번역 및 원문 검토 메모

## 작업 범위와 산출물

제공된 한글 Markdown 문서 23개를 기술 매뉴얼 형식의 영어로 번역했습니다. 원래 파일명, 제목 계층, 절 번호, 문서 간 이동 링크, 표, 주의·참고 표시, 설정값 및 이미지 참조를 유지했습니다. 링크가 유지되도록 `13_cluster_compoments.md` 파일명은 의도적으로 변경하지 않았습니다. 한글 원본 파일은 수정하지 않았습니다.

`02_english` 디렉터리에는 원본 `images` 디렉터리를 바이트 단위로 동일하게 복사했습니다. 스크린샷은 제공된 상태로 유지했으며, 이미지 안의 텍스트는 번역하거나 편집하지 않았습니다. ZIP에는 영문 매뉴얼, 이미지 파일 및 영문 검토 메모가 포함되어 있습니다.

이번 작업은 제공된 문서의 번역입니다. Terraform 패키지, OCI 서비스 지원 여부, 기본 설정값 또는 배포 절차에 대한 기술 검증을 수행한 것은 아닙니다. 이 작업에서는 Terraform 패키지를 수정하거나 배포하지 않았습니다.

## 문서 편집상 수정 사항

* README, 프로파일별 표, 워커 이미지 설정 및 부록에서 잘못된 Markdown 표 구분자를 수정했습니다. 부록의 이미지 URI 표에서 반복된 표 머리글과 구분선을 제거했습니다. 설정 항목에 해당하는 행은 삭제하지 않았습니다.
* `01_deployment_profile_and_prerequisites.md`의 잘못된 이동 링크 3개를 수정했습니다. `05_identity_and_access.md`는 `08_identity_network.md`로, `17_stack_behavior_preview_basic_mode.md`는 `20_stack_behavior.md`로, `18_plan_aply_verify.md`는 `21_plan_apply_validation.md`로 연결되도록 변경했습니다.
* 의도한 용어가 명확한 경우에는 문장, 철자 및 대소문자 오류를 수정했습니다. 여기에는 worker, Grafana, OCI Resource Manager, NFD 및 OCI HPC OKE Utils 등이 포함됩니다. 2장의 원문에 있는 “Image image”와 “Creatre in compartment”는 각각 “Import image”와 “Create in compartment”로 번역했습니다.
* 프로파일 1의 중복된 ORM Private Endpoint 행은 Markdown 구문 오류가 아닌 원문 내용의 중복이므로 그대로 유지했습니다.

## 기술 검토가 필요한 원문 사항

아래 기술 내용은 원문 그대로 보존했습니다. 매뉴얼을 확정된 배포 절차로 사용하기 전에, 배포용으로 승인된 Terraform 패키지 및 대상 Alloy 환경과 대조하여 불일치를 해소해야 합니다.

| 원본 파일 / 위치 | 검토 사항 |
|---|---|
| `04_select_deployment_profile.md`, Cluster Components | 개요에서는 모든 프로파일의 선택적 Alloy 기능 활성화 항목을 Disabled로 설정하지만, 이후 프로파일별 설명에서는 관리형 애드온을 위해 조건부로 활성화하도록 안내합니다. 또한 개요에서는 프로파일 2의 Network Operator advanced options 사용을 권장하지만, 프로파일 2와 10장에서는 RDMA를 사용하지 않는 경우 Network Operator를 비활성화하도록 안내합니다. |
| `05_profile_1.md`, 선택적 기능 활성화 항목 | 원문은 “Enable operational Alloy features after base validation”이라는 이름을 사용하며, 관리형 NFD/GPU 구성 경로가 지원되지 않는다고 설명합니다. 다른 파일에서는 “Enable optional Alloy features after base validation”이라는 이름을 사용하고, 지원되는 설치 경로로 설명합니다. 서로 다른 항목 이름과 설명을 그대로 유지했습니다. |
| `06_profile_2.md`, Create CPU pool | 원문은 Create CPU pool 항목을 “범용 GPU Worker Pool”로 설명합니다. 영문 번역에서도 GPU를 유지했으므로, 실제 풀 유형을 확인해야 합니다. |
| `07_profile_3.md`, 필수 활성화/비활성화 항목 | Create GPU pool이 두 목록에 모두 포함되어 있습니다. 상세 표와 비활성화 목록에서는 두 번째 비 RDMA GPU 풀이 명시적으로 승인된 경우를 제외하고 표준 GPU 풀을 비활성화하도록 설명합니다. 이 모순은 그대로 유지했습니다. |
| `07_profile_3.md`, Network Operator 행 | 프로파일 설정값은 Disabled이지만, 설명에서는 관리형 Network Operator를 사용하려면 활성화하도록 안내합니다. |
| `07_profile_3.md`, Lustre 행 | FSS 안내와 `/mnt/oci-fss` 예시가 반복되어 있습니다. 의도한 Lustre 전용 지침을 확인해야 합니다. |
| `13_cluster_compoments.md`, GPU/Network 제어 항목 | 표에는 advanced options 제어 항목이 있지만, 설치 여부를 제어하는 별도 행이 없습니다. 부록에서는 Install NVIDIA GPU Operator add-on과 Install NVIDIA Network Operator add-on을 각각 별도의 제어 항목으로 설명합니다. |
| `18_gpu_memory_cluster_storage.md`, FSS mount point | 예시 경로가 `/mnt/ocifss`로 되어 있지만, 다른 절에서는 `/mnt/oci-fss`를 사용합니다. 두 원문 경로를 모두 유지했습니다. |
| `20_stack_behavior.md`, Bastion boot volume size | 원문은 이 입력 항목을 Stack Behavior에 배치합니다. 번역에서는 제공된 문서 외의 대화에서 논의한 UI 변경을 적용하지 않고 원문의 배치를 유지했습니다. |
| `21_plan_apply_validation.md`, Plan 검토 | 프로파일별 항목에서는 관리형 NFD/GPU 옵션의 활성화를 허용하지만, 뒤쪽 항목에서는 두 관리형 구성 경로를 모두 비활성화 상태로 유지하도록 요구합니다. |
| `appendix_stack_items.md`, 3절 | 원문은 `0.0.0.0`이 모든 출발지 IP를 허용한다고 설명합니다. 이를 그대로 유지했지만 검토가 필요합니다. 매뉴얼의 다른 부분에서 사용하는 모든 IPv4 주소에 대한 CIDR 표기는 `0.0.0.0/0`입니다. |
| `appendix_stack_items.md`, 5.2절, Override existing installation | 원문은 Override existing NVIDIA Network Operator installation 항목에서 리소스 삭제 동작을 설명합니다. 기존 설치를 덮어쓰는 동작에 대한 설명 대신 삭제 동작을 중복 설명하고 있습니다. |
| `appendix_stack_items.md`, 5.2절 기본값 | 원문은 CNI binary directory와 애드온 버전 입력란을 비워 두면 OKE 기본값을 사용한다고 설명합니다. 실제 패키지가 빈 값을 허용하는지, 아니면 명시적인 기본값을 제공하는지 확인해야 합니다. |
| `appendix_stack_items.md`, 8.1절 | 표준 GPU 풀 표에 CPU 워커 설명과 CPU worker pool image compartment 항목 이름이 반복되어 있습니다. 원문의 용어를 그대로 유지했습니다. |
| `appendix_stack_items.md`, 10.1절 | GMC의 Shape 행에서 RDMA 워커를 설명하며, `BM.GPU.B200-v3.4`를 명시합니다. 이 값을 그대로 유지했으며, 지원되는 GMC 셰이프와 대조하여 확인해야 합니다. |
| `appendix_stack_items.md`, 11절 | 원문은 로컬 RAID 마운트 지점을 `/mnt/home`으로 지정합니다. 이 경로를 그대로 유지했으며, 실제 초기화 스크립트와 대조하여 확인해야 합니다. |
| `02_prepare_custom_image.md`, 2.2절 | 원문은 콘솔 탐색 메뉴가 오른쪽 위에 있다고 설명합니다. 3장에서는 왼쪽 위에 있다고 설명합니다. UI 확인 전까지 두 방향 설명을 그대로 유지했습니다. |
| `03_create_resource_manager_stack_load.md` | 원문은 `oci-oke-alloy-master-r1.zip` 사용을 명시적으로 요구합니다. 번역에서도 해당 패키지 이름을 유지했으며, 이후 수정된 ZIP 파일로 대체하지 않았습니다. |

## 검증

자동 검사로 다음 사항을 확인했습니다. 문서 23개의 파일명, 제목 수준, 이미지 참조 순서 및 주의·참고 표시를 원문과 비교했습니다. 영문 본문에 남아 있는 한글을 검사하고, Markdown 표의 열 수와 로컬 링크 대상을 확인했습니다. 복사한 이미지의 해시값을 비교하고 ZIP 무결성을 검사했습니다. 검사 결과는 이 파일과 같은 디렉터리의 `translation_validation.json`에 기록되어 있습니다.

원본 파일에는 코드 펜스로 감싼 코드 블록이 없습니다. 위의 문서 편집상 수정 사항을 제외하고, 기술 설정값, 식별자, 명령어, 파일 경로 및 외부 URL을 유지했습니다. 번역 작업의 일부로 외부 웹사이트나 실제 배포 동작을 재검증하지는 않았습니다.
