qqbot娱乐 使用[库](https://github.com/pandolia/qqbot)
```
# -*- coding: utf-8 -*-

import random
import time
import urllib
import urllib2
import re
import json

def reportTime():
	return '现在是北京时间' + time.strftime('%H时%M分%S秒')

def getMenu():
	menus = [
		'生活百科\n',
		'数字计算\n',
		'问答百科\n',
		'语料库\n',
		'中英互译\n',
		'笑话大全\n',
		'故事大全\n',
		'星座运势\n',
		'新闻资讯\n',
		'脑筋急转弯\n',
		'歇后语\n',
		'顺口溜\n',
		'天气查询\n',
		'菜谱大全\n',
		'列车查询\n',
		'汽油报价'
	]
	return ''.join(menus)

def tulingBot(content):
	keyword = content.split('[@ME]', 1)[1].strip()
	if keyword == '':
		replies = [
			'你倒是说话啊',
			'哼 不说话不理你了',
			'怎么只@不说话的'
		]
		return replies[random.randint(0, 2)]
	else:
		postData = {
			'reqType':0,
		    'perception': {
		        'inputText': {
		            'text': keyword
		        }
		    },
		    'userInfo': {
		        'apiKey': '8386e72ee7724afab592149ccefada1b',
		        'userId': '321871'
		    }
		}
		url = 'http://openapi.tuling123.com/openapi/api/v2'
		data = json.dumps(postData)
		req = urllib2.Request(url, data)
		resonpse = json.loads(urllib2.urlopen(req).read())
		reply = resonpse['results'][0]['values']['text']
		return reply

def onQQMessage(bot, contact, member, content):
	if bot.isMe(contact, member):
		pass
	else:
		if '@ME' in content and '报时' in content:
			# 报时
			bot.SendTo(contact, reportTime())
		elif '@ME' in content and '菜单' in content:
			#菜单
			bot.SendTo(contact, getMenu())
		elif '@ME' in content:
			#图灵机器人
			bot.SendTo(contact, tulingBot(content))
		# elif '@ME' in content and '百度' in content:
		# 	# 百度百科搜索
		# 	keyword = content.split('百度', 1)[1].strip() #re.search('百度\s?(.*?)', content)
		# 	if keyword == '':
		# 		bot.SendTo(contact, '请输入关键词')
		# 	else:
		# 		url = 'https://baike.baidu.com/item/' + keyword;
		# 		req = urllib2.Request(url);
		# 		request_data = urllib2.urlopen(req).read()
		# 		pattern = '<div class="lemma-summary" label-module="lemmaSummary">(.|\n)*?</div>'
		# 		request_data = re.search(pattern, request_data)
		# 		if request_data == None:
		# 			bot.SendTo(contact, '没有数据')
		# 		else:
		# 			pattern = '<div class="para" label-module="para">(.|\n)*</div>'
		# 			htmlData = re.search(pattern, request_data.group(0))
		# 			if htmlData == None:
		# 				bot.SendTo(contact, '没有数据')
		# 			else:
		# 				matchContent = htmlData.group(0)
		# 				result = re.compile(r'<[^>]+>', re.S).sub('', matchContent)
		# 				bot.SendTo(contact, result)
		# elif '@ME' in content:
		# 	if content.split('[@ME]', 1)[1].strip() == '':
		# 		bot.SendTo(contact, '你倒是说话啊')
		# 	else:
		# 		bot.SendTo(contact, '听8懂你在说什么')
		elif '起床' in content:
			bot.SendTo(contact, 'おはよう〜 起きていますか?')
		elif random.randint(1, 20) == 2:
			# 1/10 复读
			bot.SendTo(contact, content)
		else:
			pass
'''
	if content == 'hello':
		bot.SendTo(contact, '你好，我是机器人')
	elif content == 'stop':
		bot.SendTo(contact, 'QQ机器人已关闭')
		bot.Stop()
'''



```