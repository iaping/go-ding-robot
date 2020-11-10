# Golang钉钉自定义机器开发SDK

## Installation

Go module：
```shell
import "github.com/iaping/go-dingtalk-robot"
```

手动：
```shell
go get github.com/iaping/go-dingtalk-robot
```

## Quickstart

```go
import (
    "context"
    "github.com/go-redis/redis/v8"
)

var ctx = context.Background()

func ExampleClient() {
    rdb := redis.NewClient(&redis.Options{
        Addr:     "localhost:6379",
        Password: "", // no password set
        DB:       0,  // use default DB
    })

    err := rdb.Set(ctx, "key", "value", 0).Err()
    if err != nil {
        panic(err)
    }

    val, err := rdb.Get(ctx, "key").Result()
    if err != nil {
        panic(err)
    }
    fmt.Println("key", val)

    val2, err := rdb.Get(ctx, "key2").Result()
    if err == redis.Nil {
        fmt.Println("key2 does not exist")
    } else if err != nil {
        panic(err)
    } else {
        fmt.Println("key2", val2)
    }
    // Output: key value
    // key2 does not exist
}
```

## Quickstart

```go
package main

import (
	"fmt"

	robot "github.com/iaping/go-dingtalk-robot"
	"github.com/iaping/go-dingtalk-robot/message"
)

func main() {
	ding := robot.New("xxxxx", "xxxxx")

	//text类型
	text := message.NewText()
	text.SetContent("测试text类型").SetIsAll(true).SetAtMobiles([]string{"xxxxxxxxxxx"})
	resp, err := ding.Send(text)
	if err == nil {
		fmt.Println("result:", resp.IsSuccess(), "code:", resp.GetCode(), "message:", resp.GetMessage())
	}

	//link类型
	link := message.NewLink()
	link.SetTitle("link类型")
	link.SetText("测试link类型")
	link.SetUrl("https://www.dingtalk.com")
	link.SetPic("//www.baidu.com/img/PCtm_d9c8750bed0b3c7d089fa7d55720d6cf.png")
	resp, err = ding.Send(link)
	if err == nil {
		fmt.Println("result:", resp.IsSuccess(), "code:", resp.GetCode(), "message:", resp.GetMessage())
	}

	//markdown类型
	markdown := message.NewMarkdown()
	markdown.SetTitle("markdown类型")
	markdown.SetText("#### 杭州天气 @150XXXXXXXX \n> 9度，西北风1级，空气良89，相对温度73%\n> ![screenshot](https://img.alicdn.com/tfs/TB1NwmBEL9TBuNjy1zbXXXpepXa-2400-1218.png)\n> ###### 10点20分发布 [天气](https://www.dingtalk.com) \n")
	markdown.SetIsAll(true).SetAtMobiles([]string{"xxxxxxxxxxx"})
	resp, err = ding.Send(markdown)
	if err == nil {
		fmt.Println("result:", resp.IsSuccess(), "code:", resp.GetCode(), "message:", resp.GetMessage())
	}

	//FeedCard类型
	feedCard := message.NewFeedCard()
	feedCard.SetLinks([]*message.FeedCardLinkBody{
		&message.FeedCardLinkBody{"时代的火车向前开1", "https://gw.alicdn.com/tfs/TB1ayl9mpYqK1RjSZLeXXbXppXa-170-62.png", "https://www.dingtalk.com"},
		&message.FeedCardLinkBody{"时代的火车向前开2", "https://gw.alicdn.com/tfs/TB1ayl9mpYqK1RjSZLeXXbXppXa-170-62.png", "https://www.dingtalk.com"},
		&message.FeedCardLinkBody{"时代的火车向前开3", "https://gw.alicdn.com/tfs/TB1ayl9mpYqK1RjSZLeXXbXppXa-170-62.png", "https://www.dingtalk.com"},
	})
	link4 := message.NewFeedCardLink()
	link4.SetTitle("时代的火车向前开4")
	link4.SetPic("https://gw.alicdn.com/tfs/TB1ayl9mpYqK1RjSZLeXXbXppXa-170-62.png")
	link4.SetUrl("https://www.dingtalk.com")
	feedCard.AddLink(link4)
	resp, err = ding.Send(feedCard)
	if err == nil {
		fmt.Println("result:", resp.IsSuccess(), "code:", resp.GetCode(), "message:", resp.GetMessage())
	}

	//ActionCard类型
	//整体跳转ActionCard类型
	actionCard1 := message.NewActionCard()
	actionCard1.SetTitle("乔布斯 20 年前想打造一间苹果咖啡厅，而它正是 Apple Store 的前身")
	actionCard1.SetText("![screenshot](https://gw.alicdn.com/tfs/TB1ut3xxbsrBKNjSZFpXXcXhFXa-846-786.png) ### 乔布斯 20 年前想打造的苹果咖啡厅 Apple Store 的设计正从原来满满的科技感走向生活化，而其生活化的走向其实可以追溯到 20 年前苹果一个建立咖啡馆的计划")
	actionCard1.SetBtnTitle("测试按钮")
	actionCard1.SetBtnUrl("https://www.dingtalk.com")
	resp, err = ding.Send(actionCard1)
	if err == nil {
		fmt.Println("result:", resp.IsSuccess(), "code:", resp.GetCode(), "message:", resp.GetMessage())
	}
	//独立跳转ActionCard类型
	actionCard2 := message.NewActionCard()
	actionCard2.SetTitle("乔布斯 20 年前想打造一间苹果咖啡厅，而它正是 Apple Store 的前身")
	actionCard2.SetText("![screenshot](https://gw.alicdn.com/tfs/TB1ut3xxbsrBKNjSZFpXXcXhFXa-846-786.png) ### 乔布斯 20 年前想打造的苹果咖啡厅 Apple Store 的设计正从原来满满的科技感走向生活化，而其生活化的走向其实可以追溯到 20 年前苹果一个建立咖啡馆的计划")
	actionCard2.SetBtns([]*message.ActionCardBtnBody{
		&message.ActionCardBtnBody{"测试按钮1", "https://www.dingtalk.com"},
	})
	btn4 := message.NewActionCardBtn()
	btn4.SetTitle("测试按钮2")
	btn4.SetUrl("https://www.dingtalk.com")
	actionCard2.SetBtnOrientation(message.BtnOrientationHorizontal)
	actionCard2.AddBtn(btn4)
	resp, err = ding.Send(actionCard2)
	if err == nil {
		fmt.Println("result:", resp.IsSuccess(), "code:", resp.GetCode(), "message:", resp.GetMessage())
	}
}
```

## Reference
https://ding-doc.dingtalk.com/doc#/serverapi2/qf2nxq