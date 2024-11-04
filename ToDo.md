ToDo:
1. What is significance of Meta in django models
2. Understand this code:
    ```html
    {% for message in chat_messages %}
        {% if message.author == user %}
        <li class="flex justify-end mb-4">
            <div class="bg-green-200 rounded-l-lg rounded-tr-lg p-4 max-w-[75%]">
                <span>{{ message.body }}</span>
            </div>
            <div class="flex items-end">
                <svg height="13" width="8" >
                    <path fill="#bbf7d0" d="M6.3,10.4C1.5,8.7,0.9,5.5,0,0.2L0,13l5.2,0C7,13,9.6,11.5,6.3,10.4z"/>
                </svg>
            </div>
        </li>
        {% else %}
        <li>
            <div class="flex justify-start">
                <div class="flex items-end mr-2" >
                    <a href="{% url 'profile' message.author.username %}">
                        <img class="w-8 h-8 rounded-full object-cover" src="{{ message.author.profile.avator }}">
                    </a>
                </div>
                <div class="flex items-end" >
                    <svg height="13" width="8" >
                        <path fill="white" d="M2.8,13L8,13L8,0.2C7.1,5.5,6.5,8.7,1.7,10.4C-1.6,11.5,1,13,2.8,13z"></path>
                    </svg>
                </div>
                <div class="bg-white p-4 max-w-[75%] rounded-r-lg rounded-tl-lg">
                    <span>{{ message.body }}</span>
                </div>  
            </div>
            <div class="text-sm font-light py-1 ml-10">
                <span class="text-white">{{ message.author.profile.name }}</span> 
                <span class="text-gray-400">@{{ message.author.username }}</span>
            </div>
        </li>
        {% endif %}
        {% endfor %}
    ```
3. `related_name` field in foreign key is used to refer objects in reverse search.
e.g.
```python
# models.py
# Create your models here.
class ChatGroup(models.Model):
    group_name = models.CharField(max_length=128, unique=True)

    def __str__(self):
        return self.group_name

class GroupMessages(models.Model):
    group = models.ForeignKey(ChatGroup, related_name='chat_messages', on_delete=models.CASCADE)
    author = models.ForeignKey(User, on_delete=models.CASCADE)
    body = models.CharField(max_length=300)
    created = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return f'{self.author.username} : {self.body}'
    
    class Meta:
        ordering = ['-created']

# views.py
# Create your views here.
def chat_view(request):
    chat_group = get_object_or_404(ChatGroup, group_name="public-chat")
    chat_messages = chat_group.chat_messages.all()[:30]
    return render(request, 'a_rtchat/chat.html', {'chat_messages': chat_messages})
'''
The related_name attribute specifies the name of the reverse relation from the ChatGroup model back to GroupMessages.

If you don't specify a related_name, Django automatically creates one using the name of your model with the suffix _set, for instance ChatGroup.groupmessages_set.all().

If you do specify, e.g. related_name=chat_messages on the ChatGroup model, ChatGroup.chat_messages_set will still work, but the ChatGroup.chat_messages. syntax is obviously a bit cleaner and less clunky; so for example, if you had a user object current_chatgroup, you could use current_chatgroup.groupmessages_set.all() to get all instances of your GroupMessages model that have a relation to current_chatgroup.
'''
```