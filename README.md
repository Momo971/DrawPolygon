# DrawPolygon
用一个带两个bool值的类表示多边形上的点。
```
public class PolygonPoint
{
    public bool IsHead;
    public bool IsEnd;
    public Vector2 _point;

    public PolygonPoint(Vector2 point, bool isHead = false, bool isEnd = false)
    {
        IsHead = isHead;
        IsEnd = isEnd;
        _point = point;
    }
}
```
然后，用一个List表示多边形。
`public List<GameObject> lineList = new List<GameObject>();`


## 画点函数
通过判定lineList的Count属性是否为0来获取是否是第一个点。
通过使用IsThePoint函数来获取是否要将收尾相连，完成多边形的绘制。
```
void GetPointByClick()
    {
        if (Input.GetMouseButtonDown(0))
        {
            var p = new Vector2(Input.mousePosition.x, Input.mousePosition.y);
            Debug.Log(p);
            //起点
            if (polygon.Count == 0)
            {
                HeadPoint = p;
                BeginPointModel = GameObject.Instantiate(PointPrefab, p, Quaternion.Euler(Vector2.zero), canvas.transform) as GameObject;
                LastPointModel = GameObject.Instantiate(PointPrefab, p, Quaternion.Euler(Vector2.zero), canvas.transform) as GameObject;
                polygon.Add(new PolygonPoint(HeadPoint, isHead: true));
                LastPoint = p;
            }
            else
            {
                //终点
                if (IsTheLastPoint(p))
                {
                    polygon.Add(new PolygonPoint(p, isEnd: true));
                    DrawLineInUGUI(LastPoint, HeadPoint);
                    IsFinished = true;
                    Destroy(BeginPointModel);
                    Destroy(LastPointModel);
                }
                //中间点
                else if (p != LastPoint)
                {
                    LastPointModel.GetComponent<RectTransform>().anchoredPosition = p;
                    polygon.Add(new PolygonPoint(p));
                    DrawLineInUGUI(LastPoint, p);
                    LastPoint = p;
                }
            }
        }
    }
```

## IsTheLastPoint函数
判断最后点击位置是否为起始点
```
bool IsTheLastPoint(Vector2 _position)
    {
        var _distance = (HeadPoint - _position).sqrMagnitude;

        return _distance > EndPositionRange * EndPositionRange ? false : true;
    }
```


## 绘制直线函数
```
void DrawLineInUGUI(Vector2 v1, Vector2 v2)
    {

        Debug.Log("V1  =  " + v1 + "V2  =  " + v2);
        GameObject line = Instantiate(LinePrefab, canvas.transform) as GameObject;

        lineList.Add(line);

        var go = line.GetComponent<RectTransform>();
        go.anchoredPosition = (v1 + v2) / 2;
        go.rotation = Quaternion.Euler(0, 0, Mathf.Atan((v1.y - v2.y) / (v1.x - v2.x)) * (180 / Mathf.PI));
        go.sizeDelta = new Vector2((v2 - v1).magnitude, 2);
    }
```

## 注意：
Mathf.Atan函数返回的不是角度，而是一个小数，需要使其乘以（180/Pi）来获取你需要的旋转角度。
