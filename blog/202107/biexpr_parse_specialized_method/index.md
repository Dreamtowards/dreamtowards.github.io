
# Specialized Method for Parse Bi-Expression.
20210702

This, is a deprecated way to parse bi-expr, because it's over high-leveled, bad flexibility, unnecessary special cases.

this way is referred from "2週間でできる! スクリプト言語の作り方 / Two Weeks Homemade Scripting Language" - 千叶 滋 / チバ シゲル  (Chiba Shigeru).

but, might there still have a meanness to talk about this.

##

    parserls* m_factor;

    ast* parse_bi_expr(lexer* lex) {
        ast* left = m_factor.readone(lex);
        oper opr;
        while ((opr=find_oper(lex.peek())) != null) {
            left = read_right_side(lex, left, opr.perc);
        }
        return left;
    }

    ast* read_right_side(lexer* lex, ast* left, int opr_perc) {
        token opr_s = lex.next();
        ast* right = m_factor.readone(lex);

        oper nx_opr;
        while ((nx_opr=find_oper(lex.peek()) != null) && is_assoc_to_right(perc, nx_opr)) {
            right = read_right_side(lex, right, nx_oper.perc);
        }
        return new ast_expr_oper_bi(left, opr_s, right);
    }

    boolean is_assoc_to_right(int l_perc, oper r_opr) {
        if (r_oper.assoc == OPER_ASSOC_RIGHT)
            return r_opr.perc >= l_perc;
        else
            return r_opr.perc >  l_perc;
    }

    find_oper(string&& opr_s) {...};

    struct opr {
        int perc;
        int assoc;   // LEFT or RIGHT
        string opr;
    }