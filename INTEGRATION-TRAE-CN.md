# KYT Platform API 接口文档

## 服务信息

- **服务名称**: kyt-web-app
- **服务地址**: `http://kyt-web-app.  :10206`
- **协议**: HTTP REST
- **认证方式**: 无需认证（内部服务调用）
- **Content-Type**: application/json

---

## 接口列表

### 接口 1: 地址风险评分

**路径**: POST `/kyt-platform/internal/agent/address/risk`

**说明**: 获取指定地址的风险评分结果，包含拓扑图分析、标签风险、行为风险等综合评估

**请求参数**:

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| address | string | 是 | 钱包地址 |
| platform | string | 是 | 链平台 (ETH, BSC, TRON, MATIC, ARBITRUM, OPTIMISM, AVAX, SOL, LINEA, BASE, SCROLL, ZKSYNC, GNOSIS, KLAYTN, ZETTABLOCK, HARMONY,ron) |
| token | string | 否 | 代币地址（不传则为全币种） |
| userId | long | 是 | 用户ID |
| direction | string | 否 | 方向 (FRONT/BACK)，默认 BACK |

**请求示例**:

```python
import requests

def get_address_risk():
    url = "http://kyt-web-app.kyt-beosin-saas-test:10206/kyt-platform/internal/agent/address/risk"
    payload = {
        "address": "TDTAhGTEBHzX2SKVgz96XatVM3SBEWwW17",
        "platform": "TRON",
        "token": "TR7NHqjeKQxGTCi8q8ZY4pL8otSzgjLj6t",
        "userId": 1697507717255766016
    }
    headers = {"Content-Type": "application/json"}
    response = requests.post(url, json=payload, headers=headers, timeout=120)
    return response.json()

result = get_address_risk()
print(result)
```

**响应参数**:

| 字段 | 类型 | 说明 |
|------|------|------|
| success | boolean | 请求是否成功 |
| code | int | 状态码 |
| data | object | 风险评估结果 |
| data.totalScore | double | 总风险评分 (0-100) |
| data.totalRiskLevel | string | 总风险等级 (HIGH_RISK/MEDIUM_RISK/LOW_RISK/SAFE) |
| data.entityRisk | object | 实体风险信息 |
| data.identityRisks | array | 标签风险命中列表 |
| data.behaviorRisk | object | 行为风险信息 |
| data.inflowStrategies | array | 入账方向命中策略列表 |
| data.outflowStrategies | array | 出账方向命中策略列表 |
| data.topologyTooLarge | boolean | 拓扑图是否过大（超过500个点） |
| data.points | array | 拓扑图节点列表 |
| data.lines | array | 拓扑图连线列表 |

**响应示例**:

```json
{
  "success": true,
  "code": 200,
  "message": "success",
  "data": {
    "totalScore": 85.5,
    "totalRiskLevel": "HIGH_RISK",
    "entityRisk": {
      "isMixing": false,
      "isFinCEN": false,
      "isDarkWeb": false,
      "isGambling": false,
      "isHacked": false,
      "isSuspicious": true
    },
    "identityRisks": [
      {
        "tagType": "EXCHANGE",
        "riskType": "MEDIUM_RISK",
        "tagName": "Binance"
      }
    ],
    "behaviorRisk": {
      "normalTxCount": 150,
      "suspiciousTxCount": 3,
      "riskTxCount": 1
    },
    "inflowStrategies": [
      {
        "strategyName": "大额入金",
        "score": 20.5,
        "tag": {"name": "大额"}
      }
    ],
    "outflowStrategies": [],
    "topologyTooLarge": false,
    "points": [
      {
        "id": "node_1",
        "address": "TDTAhGTEBHzX2SKVgz96XatVM3SBEWwW17",
        "platform": "TRON",
        "depth": 0,
        "iniPoint": true
      }
    ],
    "lines": []
  }
}
```

---

### 接口 2: 交易风险评分

**路径**: POST `/kyt-platform/internal/agent/currency/risk`

**说明**: 获取指定交易的风险评分结果

**请求参数**:

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| platform | string | 是 | 链平台 (ETH, BSC, TRON, MATIC, ARBITRUM, OPTIMISM, AVAX, SOL, LINEA, BASE, SCROLL, ZKSYNC, GNOSIS, KLAYTN, ZETTABLOCK, HARMONY, ron) |
| hash | string | 是 | 交易哈希 |
| direction | string | 否 | 方向 (FRONT/BACK)，默认 BACK |
| token | string | 否 | 代币地址 |
| currencyPlatform | string | 否 | 代币链平台 |
| userId | long | 是 | 用户ID |

**请求示例**:

```python
import requests

def get_currency_risk():
    url = "http://kyt-web-app.kyt-beosin-saas-test:10206/kyt-platform/internal/agent/currency/risk"
    payload = {
        "platform": "ETH",
        "hash": "0xd0ee377f3dad7e0157920fe0cd05adc5a23cc3ded1c2b35969b5149148b6ac92",
        "direction": "FRONT",
        "userId": 1697507717255766016
    }
    headers = {"Content-Type": "application/json"}
    response = requests.post(url, json=payload, headers=headers, timeout=120)
    return response.json()

result = get_currency_risk()
print(result)
```

**响应参数**:

| 字段 | 类型 | 说明 |
|------|------|------|
| success | boolean | 请求是否成功 |
| code | int | 状态码 |
| data | object | 交易风险评估结果 |
| data.totalScore | double | 总风险评分 (0-100) |
| data.totalRiskLevel | string | 总风险等级 (HIGH_RISK/MEDIUM_RISK/LOW_RISK/SAFE) |
| data.entityRisk | object | 实体风险信息 |
| data.identityRisks | array | 标签风险命中列表 |
| data.behaviorRisk | object | 行为风险信息 |
| data.strategies | array | 命中策略列表 |

**响应示例**:

```json
{
  "success": true,
  "code": 200,
  "message": "success",
  "data": {
    "totalScore": 62.3,
    "totalRiskLevel": "MEDIUM_RISK",
    "entityRisk": {
      "isMixing": false,
      "isFinCEN": false,
      "isDarkWeb": false,
      "isGambling": false,
      "isHacked": false,
      "isSuspicious": false
    },
    "identityRisks": [
      {
        "tagType": "EXCHANGE",
        "riskType": "LOW_RISK",
        "tagName": "Coinbase"
      }
    ],
    "behaviorRisk": {
      "normalTxCount": 45,
      "suspiciousTxCount": 1,
      "riskTxCount": 0
    },
    "strategies": [
      {
        "strategyName": "可疑时间交易",
        "score": 15.0,
        "tag": {"name": "可疑"}
      }
    ]
  }
}
```

---

## 完整 Python 调用示例

```python
import requests
from typing import Optional, Dict, Any

class KytPlatformClient:
    """KYT Platform API 客户端"""

    def __init__(self, base_url: str = "http://kyt-web-app.kyt-beosin-saas-test:10206", timeout: int = 120):
        self.base_url = base_url.rstrip("/")
        self.timeout = timeout

    def get_address_risk(
        self,
        address: str,
        platform: str,
        userId: int,
        token: Optional[str] = None,
        direction: Optional[str] = "BACK"
    ) -> Dict[str, Any]:
        """
        获取地址风险评分

        Args:
            address: 钱包地址
            platform: 链平台 (ETH, BSC, TRON, MATIC, ARBITRUM, OPTIMISM, AVAX, SOL, LINEA, BASE, SCROLL, ZKSYNC, GNOSIS, KLAYTN, ZETTABLOCK, HARMONY, ron)
            userId: 用户ID
            token: 代币地址 (可选，不传则为全币种)
            direction: 方向 BACK/FRONT (可选，默认 BACK)

        Returns:
            API 响应结果
        """
        url = f"{self.base_url}/kyt-platform/internal/agent/address/risk"
        payload = {
            "address": address,
            "platform": platform.upper(),
            "userId": userId,
            "direction": direction
        }
        if token:
            payload["token"] = token

        response = requests.post(url, json=payload, timeout=self.timeout)
        return response.json()

    def get_currency_risk(
        self,
        platform: str,
        hash: str,
        userId: int,
        direction: Optional[str] = "BACK",
        token: Optional[str] = None,
        currencyPlatform: Optional[str] = None
    ) -> Dict[str, Any]:
        """
        获取交易风险评分

        Args:
            platform: 链平台 (ETH, BSC, TRON, MATIC, ARBITRUM, OPTIMISM, AVAX, SOL, LINEA, BASE, SCROLL, ZKSYNC, GNOSIS, KLAYTN, ZETTABLOCK, HARMONY, ron)
            hash: 交易哈希
            userId: 用户ID
            direction: 方向 BACK/FRONT (可选，默认 BACK)
            token: 代币地址 (可选)
            currencyPlatform: 代币链平台 (可选)

        Returns:
            API 响应结果
        """
        url = f"{self.base_url}/kyt-platform/internal/agent/currency/risk"
        payload = {
            "platform": platform.upper(),
            "hash": hash,
            "userId": userId,
            "direction": direction
        }
        if token:
            payload["token"] = token
        if currencyPlatform:
            payload["currencyPlatform"] = currencyPlatform.upper()

        response = requests.post(url, json=payload, timeout=self.timeout)
        return response.json()


if __name__ == "__main__":
    client = KytPlatformClient()

    # 地址风险评分示例
    address_result = client.get_address_risk(
        address="TDTAhGTEBHzX2SKVgz96XatVM3SBEWwW17",
        platform="TRON",
        userId=1697507717255766016,
        token="TR7NHqjeKQxGTCi8q8ZY4pL8otSzgjLj6t"
    )
    print("地址风险评分结果:")
    print(f"  总分: {address_result.get('data', {}).get('totalScore')}")
    print(f"  风险等级: {address_result.get('data', {}).get('totalRiskLevel')}")

    # 交易风险评分示例
    tx_result = client.get_currency_risk(
        platform="ETH",
        hash="0xd0ee377f3dad7e0157920fe0cd05adc5a23cc3ded1c2b35969b5149148b6ac92",
        userId=1697507717255766016,
        direction="FRONT"
    )
    print("\n交易风险评分结果:")
    print(f"  总分: {tx_result.get('data', {}).get('totalScore')}")
    print(f"  风险等级: {tx_result.get('data', {}).get('totalRiskLevel')}")
```

---

## 风险等级说明

| 风险等级 | 分值范围 | 说明 |
|---------|---------|------|
| SAFE | 0-30 | 低风险，正常使用 |
| LOW_RISK | 31-50 | 较低风险，建议关注 |
| MEDIUM_RISK | 51-70 | 中等风险，需要注意 |
| HIGH_RISK | 71-100 | 高风险，谨慎处理 |

---

## 注意事项

1. **超时设置**: 建议设置 120 秒超时，因为风险分析可能需要较长时间

2. **平台枚举值**:
   - ETH, BSC, TRON, MATIC
   - ARBITRUM, OPTIMISM, AVAX, SOL
   - LINEA, BASE, SCROLL, ZKSYNC
   - GNOSIS, KLAYTN, ZETTABLOCK, HARMONY, RON

3. **方向说明**:
   - BACK: 出账方向（从目标地址向外）
   - FRONT: 入账方向（向目标地址）

4. **错误码**: 失败时返回 `success: false`，错误信息在 `message` 字段中
