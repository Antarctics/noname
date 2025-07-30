# 附录B：共享手牌技能示例

## 1. 技能描述
- 锁定技，拥有此技能的角色共享手牌，且手牌互相可见。当你处于弃牌阶段时，你无法弃置“共享”牌。

## 2. 技能代码

- 屎山代码警告
- 仅经过浅测，仅供参考！

```javascript
"共享": {
    init(player, skill) {
        lib.translate["共享"] = "共享"
        lib.translate["共享_info"] = "锁定技，拥有此技能的角色共享手牌，且手牌互相可见。当你处于弃牌阶段时，你无法弃置“共享”牌。"
        _status.gongxiang = _status.gongxiang || {
            cards: [],
            players: []
        };
        ui.gongxiang = ui.gongxiang || ui.create.div("#gongxiang");
        for (let card of player.getCards("h")) {
            if (_status.gongxiang.cards.some(g =>
                g.name === card.name && g.suit === card.suit && g.number === card.number)) continue;
            _status.gongxiang.cards.push(card)
        }
        _status.gongxiang.players.push(get.translation(player.name))
        get.event().trigger("gongxiang_update")
    },
    mark: true,
    direct: true,
    charlotte: true,
    intro: {
        content () {
            return "当前共享手牌的角色：</br>" + _status.gongxiang.players
        }
    },
    mod: {
        ignoredHandcard (card, player) {
            return card.hasGaintag('共享')
        },
        cardDiscardable(card, player, name) {
            if (name == "phaseDiscard") return !card.hasGaintag('共享')
            return true
        },
    },
    ai: {
        viewHandcard: true,
        skillTagFilter(player, tag, arg) {
            if (arg == player) return false
            if (arg.hasSkill("共享_update")) return true
            return false
        }
    },
    onremove:function(player,skill){
        //处理自己手中的“共享”牌
        var cards = player.getCards("h", i => {
                return i.hasGaintag("共享");
            });
        player.lose(cards,ui.gongxiang)

        //处理其他人手中的“共享牌”
        const playerCards = player.getCards("h").filter(c => _status.gongxiang.cards.includes(c));
        game.players.forEach(p =>{
            if(p == player)return;
            var ccards = p.getCards("h").filter(c =>
                playerCards.some(cc => 
                    c.name === cc.name &&
                    c.suit === cc.suit &&
                    c.number === cc.number
                )
            )
            p.lose(ccards,ui.gongxiang)
        })

        //移除标记
        _status.gongxiang.cards = _status.gongxiang.cards.filter(g =>
                    !playerCards.some(card =>
                        g.name === card.name &&
                        g.suit === card.suit &&
                        g.number === card.number
                    )
                )
        _status.gongxiang.players = _status.gongxiang.players.filter(p => p!=get.translation(player.name))
    },
    group: ["共享_lose", "共享_gain", "共享_update", "共享_die"],
    subSkill: {
        lose: {
            charlotte: true,
            firstDo:true,
            direct: true,
            trigger: {
                player: ['useCardBefore', 'respondBefore', "loseBegin", "addToExpansionBegin"]
            },
            filter (event, player) {
                if (!event.cards || !event.cards.length) return false;
                return event.cards.some(card =>
                    _status.gongxiang.cards.some(g =>
                        g.name === card.name &&
                        g.suit === card.suit &&
                        g.number === card.number
                    )
                );
            },
            content() {
                //获取事件中的"共享牌"
                const sharedCards = trigger.cards.filter(card =>
                    _status.gongxiang.cards.some(g =>
                        g.name === card.name &&
                        g.suit === card.suit &&
                        g.number === card.number
                    )
                );
                //收集事件中的非"共享牌"
                var ccards = []
                ccards= trigger.cards.filter(card =>
                            !sharedCards.some(c =>
                                c.name === card.name &&
                                c.suit === card.suit &&
                                c.number === card.number
                            )
                        );
                //找到真实牌，将共享牌移除游戏
                game.players.forEach(p => {
                    const playerCards = p.getCards("h").filter(card =>
                        sharedCards.some(c =>
                            c.name === card.name &&
                            c.suit === card.suit &&
                            c.number === card.number
                        )
                    );
                    if (playerCards.some(c => _status.gongxiang.cards.includes(c))) {
                        var cards = playerCards.filter(
                                card => _status.gongxiang.cards.includes(card)
                            )
                        ccards = ccards.concat(cards)         
                    } 
                    p.lose(playerCards, ui.gongxiang)
                })
                if (event.triggername == "useCardBegin") {
                    trigger.cards = ccards
                }
                if (event.triggername == "addToExpansionBegin") {
                    trigger.cards = ccards
                }
                _status.gongxiang.cards = _status.gongxiang.cards.filter(g =>
                    !sharedCards.some(card =>
                        g.name === card.name &&
                        g.suit === card.suit &&
                        g.number === card.number
                    )
                );
            }
        },
        gain: {
            trigger: {
                player: ["gainAfter"],
            },
            charlotte: true,
            direct: true,
            firstDo:true,
            filter(event, player) {
                return player.getCards("h").some(card => !card.hasGaintag('共享'))
            },
            content: async (event, trigger, player) => {
                let cards = player.getCards("h").filter(card => !card.hasGaintag('共享'))
                for (let card of cards) {
                    if (!_status.gongxiang.cards.some(g =>
                        card.name == g.name && card.suit == g.suit && card.number == g.number)) {
                        _status.gongxiang.cards.push(card);
                    }
                }
                event.trigger("gongxiang_update")
            }
        },
        update: {
            trigger: {
                global: ["gongxiang_update"],
            },
            charlotte: true,
            direct: true,
            firstDo:true,
            filter(event, player) {
                var cards = player.getCards('h')
                let less = _status.gongxiang.cards.some(g =>
                    !cards.some(card =>
                        card.name === g.name && card.suit === g.suit && card.number === g.number
                    )
                );
                return less
            },
            content: async (event, trigger, player) => {
                var cards = player.getCards('h')
                let less = _status.gongxiang.cards.filter(g =>
                    !cards.some(card =>
                        card.name === g.name && card.suit === g.suit && card.number === g.number
                    )
                );
                cards = []
                for (let card of less) {
                    cards.push(game.createCard(card))
                }
                if (less) {
                    player.gain(cards, "bySelf").gaintag.add("共享")
                }
            }
        },
        die:{
            trigger:{
                global:"die",
            },
            charlotte: true,
            firstDo:true,
            direct: true,
            filter:function(event,player){
                return event.player.hasSkill("共享");
            },
            content(){
                trigger.player.removeSkill("共享");
            },
        },
    }
},
```
