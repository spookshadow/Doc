表达式树



>>>print-out-linq-expression-tree-hierarchy https://stackoverflow.com/questions/697463/print-out-linq-expression-tree-hierarchy

>>>> Q

int a = 1;
int b = 2;
Expression<Func<int, int>> expression = (c) => a + (b * c)
expression.Evaluate(5, stringBuilder)


Outputs:

(5) => a + (b * c) = 11 Where
     a = 1
     b * c = 10 Where
          b = 2
          c = 5

>>>> A

using System;
using System.Linq;
using System.Linq.Expressions;
using System.Text;
using System.Text.RegularExpressions;

static class Program
{
    static void Main()
    {
        int a = 1, b = 2;
        StringBuilder sb = new StringBuilder();
        Expression<Func<int, int>> expression = (c) => a + (b * c);
        expression.Evaluate(sb, 5);
        // now fix the capture class names (from a and b)
        string s = sb.ToString();
        s = Regex.Replace(s, @"value\([^)]+\)\.", "");
        Console.WriteLine(s);
    }
    public static void Evaluate(this LambdaExpression expr, StringBuilder builder, params object[] args)
    {
        var parameters = expr.Parameters.ToArray();
        if (args == null || parameters.Length != args.Length) throw new ArgumentException("args");
        Evaluate(expr.Body, 0, builder, parameters, args);
    }
    private static StringBuilder Indent(this StringBuilder builder, int depth)
    {
        for (int i = 0; i < depth; i++) builder.Append("  ");
        return builder;
    }
    private static void Evaluate(this Expression expr, int depth, StringBuilder builder, ParameterExpression[] parameters, object[] args)
    {
        builder.Indent(depth).Append(expr).Append(" = ").Append(Expression.Lambda(expr, parameters).Compile().DynamicInvoke(args));

        UnaryExpression ue;
        BinaryExpression be;
        ConditionalExpression ce;

        if ((ue = expr as UnaryExpression) != null)
        {
            builder.AppendLine(" where");
            Evaluate(ue.Operand, depth + 1, builder, parameters, args);
        }
        if ((be = expr as BinaryExpression) != null)
        {
            builder.AppendLine(" where");
            Evaluate(be.Left, depth + 1, builder, parameters, args);
            Evaluate(be.Right, depth + 1, builder, parameters, args);                   
        }
        else if ((ce = expr as ConditionalExpression) != null)
        {
            builder.AppendLine(" where");
            Evaluate(ce.Test, depth + 1, builder, parameters, args);
            Evaluate(ce.IfTrue, depth + 1, builder, parameters, args);
            Evaluate(ce.IfFalse, depth + 1, builder, parameters, args);
        }
        else
        {
            builder.AppendLine();
        }
    }

}


>>> Expression Trees (C#) 
https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/expression-trees/

>>> functional-csharp-function-as-data-and-expression-tree

https://weblogs.asp.net/dixin/functional-csharp-function-as-data-and-expression-tree


>>> c# 扩展方法奇思妙用基础篇九：Expression 扩展
https://www.cnblogs.com/ldp615/archive/2011/09/15/expression-extension-methods.html

