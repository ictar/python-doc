原文：[Wedding at Scale: How I Used Twilio, Python and Google to Automate My Wedding](https://www.twilio.com/blog/2017/04/wedding-at-scale-how-i-used-twilio-python-and-google-to-automate-my-wedding.html)

---

![](https://www.twilio.com/blog/wp-content/uploads/2017/04/10ea7e8b8795666b1abe660c865a0659-1.jpg)

2016年9月3日，对世界上的大多数人来说，或许就只是普普通通的一天，但对我而言，将会是一个难忘的日子，因为在那一天，我结婚了。

在规划婚礼时，要考虑许多不同的方面。食物、装饰、桌子装置（啊，是哒，这独立于装饰）、鲜花、住宿、交通、娱乐和位置。虽然在规划婚礼时有许许多多未知数，但是我可以肯定一件事。在婚礼中，有大量的名单、嵌套的名单、以及更多的远到目光可见的名单。当我瞪着越来越多的项目时，我开始怀疑，是否有更好的方法来处理？这一切都如此的手动，充满了低效。必须有一些技术可以改进的方面。

你可能会感到惊讶，但是邀请人们参加婚礼是_昂贵的_（超过380磅），因为你需要发送“按时出席”卡片以及随后的关于婚礼细节的邀请。这也是_缓慢的_，因为你必须通过邮寄来发送它们。追踪人们是否接收到邀请，以及他们是否想要来参加提供免费食物和饮料的派对，是非常_耗时的_，当然，一个自动的好的？最后，邀请卡_不是环境友好的_，因为它们被一次性使用，并且容易丢失或错放。

回到名单。客人名单分成几个部分：

1.  你想要他来的人的名单
2.  回复你的R.S.V.P的人的名单
3.  回复你会来的人的名单
4.  回复你回来的，并且选择了食物的人的名单

但是名单是好的。它们有预先定义好的要求和响应，这让它们是自动化的重要选择。

## 瓶中信

无关年龄，我确信婚礼名单上每个人都有手机，这意味着该是Twilio上场的时候了。如果你想要跳到代码，那么你可以看看[GitHub上的repo](https://github.com/SeekTom/Twilio/tree/master/Wedication)。

[SMS](https://www.twilio.com/docs/api/rest/sending-messages)对我的需求而言相当完美。我可以配置发出的群发短信，并且快速有效地处理回应。在绘制一个MVP并且考虑数据库的时候，我想要某些易于分享的东西，并且不想要浪费时间来构建视图。偶然发现的gspread python库使得我能够[_读写谷歌电子表格_](https://www.twilio.com/blog/2017/02/an-easy-way-to-read-and-write-to-a-google-spreadsheet-in-python.html)。虽然这不是最快的选择，但它确实足够灵活，并且提供了一个易于访问和可读的输出。

对于初始的R.S.V.P，[我创建了一个电子表格](https://docs.google.com/spreadsheets/d/1Zud0nYlAQw7RywwiDmADf9Cd3bBTsHaUSQYXh_Cl9_w/edit?usp=sharing)，包含这些列：

*   Name
*   Telephone_number
*   Confirmation_status
*   Contact detail status
*   Message_count (发送给客人的邮件数，稍后它会派上用场)

主要数据输入完成后，我使用[gspread](http://gspread.readthedocs.io/en/latest/)来遍历列表，并且发送短信给每一个具有与之相关联的手机号码的客人：

[Sheets.py](https://github.com/SeekTom/Twilio/blob/master/Wedication/sheets.py)

```python
import json
import time
import gspread
from oauth2client.client import SignedJwtAssertionCredentials
from twilio.rest import TwilioRestClient

# Message your attendees from a spreadsheet

# add file name for the json created for the spreadsheet
json_key = json.load(open('.json'))
scope = ['https://spreadsheets.google.com/feeds']

credentials = SignedJwtAssertionCredentials(json_key['client_email'],
                                            json_key['private_key'].encode(),
                                            scope)
gc = gspread.authorize(credentials)
wks = gc.open("wedding_guests")  # add your workbook name here
wks_attendees = wks.get_worksheet(0)  # attendees worksheet

ACCOUNT_SID = 'TWILIO_ACCOUNT_SID'
AUTH_TOKEN = 'TWILIO_AUTH_TOKEN'

client = TwilioRestClient(ACCOUNT_SID, AUTH_TOKEN)

# to iterate between guests, amend this based on your total
for num in range(2, 60):
    print "sleeping for 2 seconds"
    time.sleep(2)  # adding a delay to avoid filtering

    guest_number = wks_attendees.acell('B'+str(num)).value
    guest_name = wks_attendees.acell('A'+str(num)).value
    Message_body = <span class="pl-s"><span class="pl-k">u</span><span class="pl-pds">"</span><span class="pl-cce">\u2B50</span><span class="pl-pds">"</span></span> <span class="pl-k">+</span> <span class="pl-s"><span class="pl-k">u</span><span class="pl-pds">"</span><span class="pl-cce">\u2764</span><span class="pl-pds">"</span></span> <span class="pl-k">+</span> <span class="pl-s"><span class="pl-k">u</span><span class="pl-pds">"</span><span class="pl-cce">\u2B50</span><span class="pl-pds">"</span></span> <span class="pl-k">+</span> <span class="pl-s"><span class="pl-k">u</span><span class="pl-pds">"</span><span class="pl-cce">\u2764</span><span class="pl-pds">"</span></span> <span class="pl-k">+</span> <span class="pl-s"><span class="pl-k">u</span><span class="pl-pds">"</span><span class="pl-cce">\u2B50</span><span class="pl-pds">"</span></span> <span class="pl-k">+</span> <span class="pl-s"><span class="pl-k">u</span><span class="pl-pds">"</span><span class="pl-cce">\u2764</span><span class="pl-pds">"</span></span> <span class="pl-k">+</span> <span class="pl-s"><span class="pl-k">u</span><span class="pl-pds">"</span><span class="pl-cce">\u2B50</span><span class="pl-pds">"</span></span> <span class="pl-k">+</span> <span class="pl-s"><span class="pl-k">u</span><span class="pl-pds">"</span><span class="pl-cce">\u2764</span><span class="pl-pds">"</span></span> <span class="pl-k">+</span> <span class="pl-s"><span class="pl-pds">"</span><span class="pl-cce">\n\n</span><span class="pl-pds">"</span></span> <span class="pl-k">+</span> <span class="pl-s"><span class="pl-k">u</span><span class="pl-pds">"</span><span class="pl-cce">\u2709</span><span class="pl-pds">"</span></span> <span class="pl-k">+</span><span class="pl-s"><span class="pl-pds">"</span> Save the date! <span class="pl-pds">"</span></span><span class="pl-k">+</span> <span class="pl-s"><span class="pl-k">u</span><span class="pl-pds">"</span><span class="pl-cce">\u2709</span><span class="pl-pds">"</span></span> <span class="pl-k">+</span><span class="pl-s"><span class="pl-pds">"</span><span class="pl-cce">\n\n</span>Lauren Pang and Thomas Curtis are delighted to invite you to our wedding.<span class="pl-cce">\n\n</span>Saturday 3rd September 2016. <span class="pl-cce">\n\n</span>Colville Hall,<span class="pl-cce">\n</span>Chelmsford Road,<span class="pl-cce">\n</span>White Roding,<span class="pl-cce">\n</span>CM6 1RQ.<span class="pl-cce">\n\n</span>The Ceremony begins at 2pm.<span class="pl-cce">\n\n</span>More details will follow shortly!<span class="pl-cce">\n\n</span>Please text YES if you are saving the date and can join us or text NO if sadly, you won't be able to be with us.<span class="pl-cce">\n\n</span><span class="pl-pds">"</span></span> <span class="pl-s"><span class="pl-k">u</span><span class="pl-pds">"</span><span class="pl-cce">\u2B50</span><span class="pl-pds">"</span></span> <span class="pl-k">+</span> <span class="pl-s"><span class="pl-k">u</span><span class="pl-pds">"</span><span class="pl-cce">\u2764</span><span class="pl-pds">"</span></span> <span class="pl-k">+</span> <span class="pl-s"><span class="pl-k">u</span><span class="pl-pds">"</span><span class="pl-cce">\u2B50</span><span class="pl-pds">"</span></span> <span class="pl-k">+</span> <span class="pl-s"><span class="pl-k">u</span><span class="pl-pds">"</span><span class="pl-cce">\u2764</span><span class="pl-pds">"</span></span> <span class="pl-k">+</span> <span class="pl-s"><span class="pl-k">u</span><span class="pl-pds">"</span><span class="pl-cce">\u2B50</span><span class="pl-pds">"</span></span> <span class="pl-k">+</span> <span class="pl-s"><span class="pl-k">u</span><span class="pl-pds">"</span><span class="pl-cce">\u2764</span><span class="pl-pds">"</span></span> <span class="pl-k">+</span> <span class="pl-s"><span class="pl-k">u</span><span class="pl-pds">"</span><span class="pl-cce">\u2B50</span><span class="pl-pds">"</span></span> <span class="pl-k">+</span> <span class="pl-s"><span class="pl-k">u</span><span class="pl-pds">"</span><span class="pl-cce">\u2764</span><span class="pl-pds">"</span></span>,
    if not guest_number:  # No mobile number skip this guest
        print guest_name + ' telephone number empty not messaging'
        wks_attendees.update_acell('E'+str(num), '0')  # set number to 0

    else:
        print 'Sending message to ' + guest_name
        client.messages.create(
            to="+" + guest_number,  # add the + back to make the number e.164
            from_="",  # your twilio number here
            body=message_body,
        )
        wks_attendees.update_acell('E'+str(num), int(wks_attendees.acell('E'+str(num)).value) + 1)  # increment the message count row
else:                  # else part of the loop
    print 'finished'
```

因为短信可以看起来很简单，所以我添加了一些[unicode](https://www.twilio.com/blog/2015/08/common-sms-problems-unicode-twilio.html)来让它们有趣些。下面是幸运的受邀者接收到的短信样式：

![](https://www.twilio.com/blog/wp-content/uploads/2017/04/9gurtth2sPDkquNPSB3DiVB6wWU0TAn5nS80zK7d3u9DG5KNsUd8GkyqDzcnsq8k7OymGdPEDR15idIl1bUC0v0_onaVe_q8OghM9iuFfWAfa0vKLLu62_PFLFQrtR8Sq86N0KZb.png)


接下来，我使用Flask作为我的web服务器，然后设置我的Twilio消息请求URL指向`/messages` url，并创建简单的if语句来解析回复 (yes, no)：

[hello_guest.py](https://github.com/SeekTom/Twilio/blob/master/Wedication/hello_guest.py)

```python
@app.route("/messages", methods=['GET', 'POST'])
def hello_guest():

    if "yes" in body_strip:
        # We have a keeper! Find the attendee and update their confirmation_status
        wks_attendees.update_acell("F"+str(guest_confirmation_cell.row), 'Accepted')  # update the status to accepted for that guest
        resp.message(u"\u2665" + "Thanks for confirming, we'll be in touch!" + u"\u2665")  # respond to the guest with a confirmation!

    elif "no" in from_body.lower():
        # update the confirmation_status row to declined for that guest
        wks_attendees.update_acell("F"+str(guest_confirmation_cell.row), 'Declined')
        # respond to the user confirming the action
        resp.message("Sorry to hear that, we still love you though!")

    else:  # respond with invalid keyword
        resp.message("You sent a different keyword, we need a yes or a no, you sent: "+  
                     from_body)
    return str(resp)
```

![IMG_4740.PNG](https://www.twilio.com/blog/wp-content/uploads/2017/04/4U8VRU8xfGnCU1MWwaE19uk4v8XG9dkiz3CA9O77YZcLcpCSZJtfos103F7TcHJuVNYjBIoKnLliDF_sy-qY0Z-XNEkiJZx_ZFxKUaW7antH8CxWSKBY3TSUYOu88j4YzZUtlpZ_.png)

![IMG_4741.PNG](https://www.twilio.com/blog/wp-content/uploads/2017/04/gKOZWbotbErMUwpwyKNrX3qh0tOEt1g60YmEerkbHQE-InNGIy5lQmCAQ8MDqMg2lnmB4CY6uZeTnhQMxeQ1Kl6m95ZxxdSFh-4FIk_pqGag69VJcgnNoeaaC7w4ihaD2zSHbKf6.png)


第一条消息是在2月19日早上8:37的时候发送的，而在3分钟后，也就是早上8:40收到了第一条回复。到了早上9:38，我收到了23条确认回复，这可是32%的接受率！初始群发短信2天后，我们收到了58%的客人的确认！尽管取得了明显的成功，但是我的未婚妻并不热衷于我那作为婚礼邀请服务(SAAWIS?)的短信，因此，我决定添加一些功能到我的应用中。

统计！我可以计算现场出席名单并按要求退回，给新娘即使反馈客人名单的成型。代码很简单，因为我已经在电子表格中设置了一些基本的计数器，因此，仅仅是抓取这些单元格的内容，并将其添加到短信中的事：

[hello_guest.py](https://github.com/SeekTom/Twilio/blob/master/Wedication/hello_guest.py)

```python
# attendance variables
guest_confirmed = wks_attendees.acell('C70').value
guest_unconfirmed = wks_attendees.acell('C71').value
guest_no_response = wks_attendees.acell('C72').value
guest_acceptance = wks_attendees.acell('C73').value


elif "numbers" in from_body.lower():
    # return statistics (total guests, food choices list)
    resp.message("R.S.V.P update:\n\nTotal Accepted: " + guest_confirmed  
                 "\n\nTotal declined: "   guest_unconfirmed   "\n\nTotal no response: "+  
                 guest_no_response + "\n\nTotal acceptance rate: " + guest_acceptance)
```

以下是最终的短信：

![IMG_4731.PNG](https://www.twilio.com/blog/wp-content/uploads/2017/04/BR-VSCfIz68d46FOVDqZBkUhRMesqRgTF_QItTof2m4LOztmhOp_aNwHFaHASCfPhrPZRYhU2yLz6YFDgqumSenrhw_xOFfXKw8Dl7hTl-j5s1fz_Gvile9jIH68m7UTkyBIxuAm.png)


不是很漂亮，但很有用。

Lauren现在可以跟踪出席率，这件事大大缓解了她的压力。从那时起，万事俱备，并且短信被尽可能集成到婚礼的方方面面。有些是显而易见的，例如当婚礼网站 (自然，由[Heroku](https://www.heroku.com)提供动力) 上线的时候发送通知短信，分享礼物列表以及其他我至今仍然感到骄傲的事。

## 食物，极好的食物

在建立R.S.V.P名单后，经常被推迟的是让客人确认他们的食物选择。你会惊讶于让人们选择免费的食物是多么的困难。第一步是发送另一条短信，告诉那些确认参与的客人访问网站，并通过一个谷歌表单选择他们的食物选项。相当标准的东西，然而，表单被设置为填充与参与者相同的工作簿。这意味着，现在，我有了已确认参与的客人以及那些填写了食物选择表格的客人表单。通常，我会等待客人慢慢选择他们的饭菜，但由于我的婚礼由Twilio驱动，意味着我可以用最少的努力来跟踪。

数据需要匹配访客名称上的两个电子表格，并且在有匹配的时候更新客人的食物选择状态。这需要一些额外的工作，但一旦重排代码，我就可以按需批量运行脚本，并最后通过短信获取我的客人的最新状态：

[food.py](https://github.com/SeekTom/Twilio/blob/master/Wedication/food.py)

```python
import json
import time
import gspread
from oauth2client.client import SignedJwtAssertionCredentials
from twilio.rest import TwilioRestClient

# add file name for the json created for the spread sheet
json_key = json.load(open(''))
scope = ['https://spreadsheets.google.com/feeds']

credentials = SignedJwtAssertionCredentials(json_key['client_email'],
                                            json_key['private_key'].encode(),
                                            scope)
gc = gspread.authorize(credentials)
wks = gc.open("")  # add your spreadsheet name here
wks_attendees = wks.get_worksheet(0)  # attendees worksheet
wks_food = wks.get_worksheet(1)  # food responses worksheet

ACCOUNT_SID = 'TWILIO_ACCOUNT_SID'
AUTH_TOKEN = 'TWILIO_AUTH_TOKEN'

client = TwilioRestClient(ACCOUNT_SID, AUTH_TOKEN)

# to iterate between 10 to 60 manual hack to ensure no guests not left out
for num in range(2, 60):
    food_guest_name = wks_food.acell('B'+str(num)).value  # food choice name column

    if food_guest_name:
        attendees_name = wks_attendees.find(val_food_guest_name).value
        attendees_name_row = wks_attendees.find(val_food_guest_name).row
        menu_status = wks_attendees.acell("G"+str(attendees_name_row)).value

        if food_guest_name == attendees_name:
            print
            if menu_status == 'Y':  # data already matched, move on
                print('Skipping')

            else:  # user has supplied their choices, update main spreadsheet
                print ('Food sheet name ' + food_guest_name + 'Attendees sheet name ' + attendees_name)
                # update menu choices row
                wks_attendees.update_acell("G"+str(attendees_name_row), 'Y')
        else:
            print('nothing found, moving on')
            wks_attendees.update_acell('E'+str(num), int(wks.acell('E'+str(num)).value) + 1)  # increment the message count row

    else:
        # send message to the admin that the process has been completed with update stats
        client.messages.create(from_="",  # twilio number here
                               to="",  # admin number here
                               body="Finished processing current meal listnnGuest meals confirmed" + guest_meals_confirmed + "\n\nGuest meals unconfirmed: " + guest_meals_unconfirmed)
```

现在，有了一个确认的客人名单和越来越多的食物选择名单，通过主要应用将这些统计数据公开是有意义的。所需的只是抓取相关单元格的内容，然后用短信回复：

[Hello_guest.py](https://github.com/SeekTom/Twilio/blob/master/Wedication/hello_guest.py)

```python
# respond with the current food totals and the meal choices
elif "food" in body_strip.strip():

    resp.message("Guest meals decided:" + guest_meals_confirmed + 
                 "\nGuest meals undecided: " + guest_meals_unconfirmed +
                 "\n\nMenu breakdown:\n\n" + starter_option_1 +": " +
                 starter_option_1_amount + "\n" + starter_option_2 +": " +
                 starter_option_2_amount + "\n" + starter_option_3 +": " +
                 starter_option_3_amount + "\n" + main_option_1 +": " +
                 main_option_1_amount + "\n" + main_option_2 +": " + main_option_2_amount +
                 "\n" + main_option_3 +": " + main_option_3_amount + "\n" +
                 dessert_option_1 + ": " + dessert_option_1_amount + "\n" + dessert_option_2
                 + ": " + dessert_option_2_amount)
```



![IMG_4733.PNG](https://www.twilio.com/blog/wp-content/uploads/2017/04/lXvZUF9yXIvjBZxD8P6OOl9tvk7n_0uK6iW25Ryyt6D__lhPUWAQx0quDH2pGkyK-bxqIDJrBWa4TPACui7BU6ev2E9V6nQZA5MIe453F2d8RWipcRO0-Hbtm6Ti8aVOl0qZ_Lw6.png)



让婚礼餐饮者了解我们的进展，并提供谁没有选择的可操作数据，是非常方便的。追踪客人是另一个自动化选择。简单遍历参加者名单，找到没有选择用餐选项的调皮的客人，然后给他们发送信息！

[Chase.py](https://github.com/SeekTom/Twilio/blob/master/Wedication/chase.py)

```python
for num in range(2, 72):  # manual hack to ensure no guests not left out
    print "sleeping for 3 seconds"

    time.sleep(3)  # adding a delay to avoid carrier filtering
    wedding_guest_number = wks_attendees.acell('B'+str(num)).value  # grab attendee tel number
    wedding_guest_name = wks_attendees.acell('A'+str(num)).value  # grab attendee name
    menu_guest = wks_attendees.acell('G'+str(num)).value

    if not wedding_guest_number:
        print wedding_guest_name+' telephone number empty not messaging'  # output to console that we are not messaging this guest due to lack of telephone number
        wks_attendees.update_acell('H'+str(num), '1')  # increment the message count row for the individual user

    else:
        if menu_guest == "N":  # guest has not chosen food! CHASE THEM!
            print 'Sending message to '+wedding_guest_name
            client.messages.create(
                to="+" + wedding_guest_number,
                from_="",  # your Twilio number here
                body="If you have received this message, you have not chosen your food options for Tom & Lauren's Wedding!\n\nYou can pick your choices via the website, no paper or postage required!\n\nhttp://www.yourwebsitehere.com/food"
            )
            wks_attendees.update_acell('H'+str(num), int(wks_attendees.acell('H'+str(num)).value) + 1)  # increment the message count row for the individual user
else:                  # else part of the loop
    print 'finished'
```



![IMG_4735.PNG](https://www.twilio.com/blog/wp-content/uploads/2017/04/kIn4nEbZa5aaOMXqk_iZJ8pv6iBgPwbdD7Nzk0zfsojmwenHCxAa_8tLFDyCPzNy_4YYbvl3UbnqWdjyQQM_RF_wRS-eF7Pfd_tzg9LYDgWPiLzA2xMkTjj77pFiT2V_pnnFSqYW.png)



大日子比我们所想的来得更快些。而唯一需要做的事就是发送最后一条短信，提醒客人基本的细节，以及提醒他们带把伞，以防碰上一般的英国夏季的雨季：


![IMG_4742.PNG](https://www.twilio.com/blog/wp-content/uploads/2017/04/fTgy4spMJ8igrc205rMO-OuRA6UeZIVxgPuhCwQ2G49aQQ18eV8KhwtP31Gtltegf-DkcTxDJTdW31R6LQAVBCSTmNf54fRrOUc18TvqULdes-lFDbTVw7Trlz8sNTRge8VUNSI1.png)

## 总结一下

婚礼永远不是个简单的事，它会让你感觉到很多事都不在你掌控之下。自动化通过提供与我们的客人的直接渠道，以及无数的我可以跟踪、推动以及戳他们回应的不同方式，显然让我的生活更轻松了。它帮助我们在婚礼臭名昭着的时间消耗方面变得积极主动，让我们可以空出来关注大日子的其他重要领域。

为复杂问题建立可扩展的解决方案从来不是件简单的事，即使在其最终形式下，我的应用有时也是很脆弱的。我已经计划建立一个更加完整的解决方案，带有进度的数据可视化、语音基础并更少依赖于CLI脚本，但是时间更重要些。总的来说，我很高兴它的工作方式。没有通讯系统是完美的。你需要实现最适合你的受众的渠道，无论是[短信](https://www.twilio.com/docs/api/rest/sending-messages)，[语音](https://www.twilio.com/docs/api/rest/making-calls)，[聊天](https://www.twilio.com/docs/api/chat)，[视频](https://www.twilio.com/docs/api/video)，还是[信号量](https://en.wikipedia.org/wiki/Semaphore_(programming))。

如果你想要聊聊关于婚礼自动化的事，我在[Twitter上的@seektom](https://twitter.com/SeekTom)等你。